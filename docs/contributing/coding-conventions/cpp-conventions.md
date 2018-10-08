# C++ Coding Conventions

This is an overview of the high-level coding conventions we use when writing Qt code.
See [C++ Coding Style](cpp-style.md) for the lower-level conventions.

For QML, see [QML Coding Conventions](qml-conventions.md).

### C++ features

- Don't use exceptions
- Don't use rtti (Run-Time Type Information; that is, the `typeinfo` struct, the `dynamic_cast` or the `typeid` operators, including throwing exceptions)
- Use templates wisely, not just because you can.

## Conventions in C++ source code

- All code is ascii only (7-bit characters only, run `man ascii` if unsure)
  - Rationale: We have too many locales inhouse and an unhealthy mix of UTF-8 and latin1 systems. Usually, characters > 127 can be broken without you even knowing by clicking SAVE in your favourite editor.
  - For strings: Use `\nnn` (where `nnn` is the octal representation of whatever character encoding you want your string in) or `\xnn` (where `nn` is hexadecimal). Example: `QString s = QString::fromUtf8("13\005");`
  - For umlauts in documentation, or other non-ASCII characters, either use qdoc's command or use the relevant macro; e.g. `\uuml` for &uuml;.
- Every QObject subclass must have a `Q_OBJECT` macro, even if it doesn't have signals or slots, otherwise `qobject_cast` will fail.
- Normalize the arguments for signals + slots (see `QMetaObject::normalizedSignature`) inside connect statements to get faster signal/slot lookups. You can use `qtrepotools/util/normalize` to normalize existing code.

### Including headers

- In public header files, always use this form to include Qt headers: `#include <QtCore/qwhatever.h>`. The library prefix is necessary for Mac OS X frameworks and is very convenient for non-qmake projects.
- In source files, include specialized headers first, then generic headers. Separate the categories with empty lines.

```cpp
#include <qstring.h> // Qt class
#include <new> // STL stuff
#include <limits.h> // system stuff
```

- If you need to include `qplatformdefs.h`, always include it as the '''first''' header file.
- If you need to include private headers, be careful. Use the following syntax, irrespective of which module or directory whatever_p.h is in.

```cpp
#include <private/whatever_p.h>
```

### Include guards

Never use `#pragma once` because it's not part of the C++ standard, it's not always supported and it's subject to double inclusion if
any headers are copied somehere in the build process (which we does).

Use include guards instead like these:

```cpp
#ifndef SOMEFILENAME_H
#define SOMEFILENAME_H

// contents

#endif // SOMEFILENAME_H
```

See the following threads from the Qt mailing list:

* [https://lists.qt-project.org/pipermail/development/2018-January/031966.html](https://lists.qt-project.org/pipermail/development/2018-January/031966.html)
* [https://lists.qt-project.org/pipermail/development/2018-October/033726.html](https://lists.qt-project.org/pipermail/development/2018-October/033726.html)

### Casting

- Avoid C casts, prefer C++ casts (`static_cast`, `const_cast`, `reinterpret_cast`)
  - Rationale: Both reinterpret_cast and C-style casts are dangerous, but at least reinterpret_cast won't remove the const modifier
- Don't use `dynamic_cast`, use `qobject_cast` for QObjects or refactor your design, for example by introducing a type() method (see QListWidgetItem)
- Use the constructor to cast simple types: `int(myFloat)` instead of `(int)myFloat`
  - Rationale: When refactoring code, the compiler will instantly let you know if the cast would become dangerous.

### Compiler/Platform specific issues

- Be extremely careful when using the questionmark operator. If the returned types aren't identical, some compilers generate code that crashes at runtime (you won't even get a compiler warning).

```cpp
QString s;
return condition ? s : "nothing"; // crash at runtime - QString vs. const char *
```

- Be extremely careful about alignment.
- Whenever a pointer is cast such that the required alignment of the target is increased, the resulting code might crash at runtime on some architectures. For example, if a `const char *` is cast to an `const int *`, it'll crash on machines where integers have to be aligned at two- or four-byte boundaries.
- Use a union to force the compiler to align variables correctly. In the example below, you can be sure that all instances of `AlignHelper` are aligned at integer-boundaries.

```cpp
union AlignHelper {
    char c;
    int i;
};
```

- Anything that has a constructor or needs to run code to be initialized cannot be used as global object in library code, since it is undefined when that constructor/code will be run (on first usage, on library load, before main() or not at all). Even if the execution time of the initializer is defined for shared libraries, you'll get into trouble when moving that code in a plugin or if the library is compiled statically:

```cpp
// global scope
static const QString x; // Wrong - default constructor needs to be run to initialize x
static const QString y = "Hello"; // Wrong - constructor that takes a const char * has to be run
QString z; // super wrong
static const int i = foo(); // wrong - call time of foo() undefined, might not be called at all
```

Things you can do:

```cpp
// global scope
static const char x[] = "someText"; // Works - no constructor must be run, x set at compile time
static int y = 7; // Works - y will be set at compile time
static MyStruct s = {1, 2, 3}; // Works - will be initialized statically, no code being run
static QString *ptr = 0; // Pointers to objects are ok - no code needed to be run to initialize ptr
```

Use `Q_GLOBAL_STATIC` to create static global objects instead:

```cpp
Q_GLOBAL_STATIC(QString, s)

void foo()
{
    s()->append("moo");
}
```

Note: Static objects in a scope are no problem, the constructor will be run the first time the scope is entered. The code is not reentrant, though.

- A `char` is signed or unsigned dependent on the architecture. Use `signed char` or `unsigned char` if you explicitely want a signed/unsigned char. The condition in the following code is always true on platforms where the default char is unsigned.

```cpp
char c; // c can't be negative if it is unsigned

if (c > 0) { … } // WRONG - condition is always true on platforms where the default is unsigned
```

- Avoid 64-bit enum values.
- The aapcs embedded ABI hard codes all enum values to a 32-bit integer.
- Microsoft compilers don't support 64-bit enum values. (confirmed with Microsoft ® C/C++ Optimizing Compiler Version 15.00.30729.01 for x64)

### Aesthetics

- Prefer enums to define constants over `static const int` or defines.
  - enum values will be replaced by the compiler at compile time, resulting in faster code
  - defines are not namespace safe (and look ugly)
- Prefer verbose argument names in headers.
  - Most IDEs will show the argument names in their completion-box.
  - It will look better in the documentation
  - Bad style: `doSomething(QRegion rgn, QPoint p)` - use `doSomething(QRegion clientRegion, QPoint gravitySource)` instead
- When reimplementing a virtual method, do not put the `virtual` keyword in the header file. On Qt5, annotate them with the [Q_DECL_OVERRIDE](http://doc.qt.io/qt-5/qtglobal.html#Q_DECL_OVERRIDE) macro after the function declaration, just before the `;` (or the `{`).

### Things to avoid

- Do not inherit from template/tool classes
- The destructors are not virtual, leading to potential memory leaks
- The symbols are not exported (and mostly inline), leading to interesting symbol clashes.
- Example: Library A has `class Q_EXPORT X: public QList<QVariant> {};` and library B has `class Q_EXPORT Y: public QList<QVariant> {};` Suddenly, QList<QVariant>'s symbols are exported from two libraries - /clash/.
- Don't mix const and non-const iterators. This will silently crash on broken compilers.

```cpp
for (Container::const_iterator it = c.begin(); it != c.end(); ++it) // W R O N G
for (Container::const_iterator it = c.cbegin(); it != c.cend(); ++it) // Right
```

- Q[Core]Application is a singleton class. There can only be one instance at a time. However, that instance can be destroyed and a new one can be created, which is likely in an ActiveQt or browser plugin. Code like this will easily break:

```cpp
static QObject *obj = 0;
if (!obj)
    obj = new QObject(QCoreApplication::instance());
```

If the `QCoreApplication` application is destroyed, `obj` will be a dangling pointer. Use `Q_GLOBAL_STATIC` for static global objects or `qAddPostRoutine` to clean up.

- Avoid the use of anonymous namespaces in favor of the static keyword if possible. A name localized to the compilation unit with `static` is guaranteed to have internal linkage. For names declared in anonymous namespaces the C++ standard unfortunately mandates external linkage. (7.1.1/6, or see various discussions about this on the gcc mailing lists)

### Binary and Source Compatibility

- Definitions:
  - Qt 4.0.0 is a major release, Qt 4.1.0 is a minor release, Qt 4.1.1 is a patch release
  - Backward binary compatibility: Code linked to an earlier version of the library keeps working
  - Forward binary compatibility: Code linked to a newer version of the library works with an older library
  - Source code compatibility: Code compiles without modification
- Keep backward binary compatibility + backward source code compatibility in minor releases
- Keep backward and forward binary compatibility + forward and backward source code compatibility in patch releases
  - Don't add/remove any public API (e.g. global functions, public/protected/private methods)
  - Don't reimplement methods (not even inlines, nor protected/private methods)
  - Check [[Binary Compatibility Workarounds]] for ways to keep b/c
- Info on binary compatibility: https://community.kde.org/Policies/Binary_Compatibility_Issues_With_C++
- When writing a QWidget subclass, always reimplement event(), even if it's empty. This makes sure that the widget can be fixed without breaking binary compatibility.
- All exported functions from Qt must start with either 'q' or 'Q'. Use the "symbols" autotest to find violations.

### Operators

["The decision between member and non-member"](http://stackoverflow.com/questions/4421706/operator-overloading/4421729#4421729)

A binary operator that treats both of its arguments equally should not be a member. Because, in addition to the reasons mentioned in the stack overflow answer, the arguments are not equal when the operator is a member.

Example with QLineF which unfortunately has its operator== as a member:

```cpp
QLineF lineF;
QLine lineN;

if (lineF == lineN) // Ok,  lineN is implicitly converted to QLineF
if (lineN == lineF) // Error: QLineF cannot be converted implicitly to QLine, and the LHS is a member so no conversion applies
```

If the operator== was outside of the class, conversion rules would apply equally for both sides.

## Conventions for public header files

Our public header files have to survive the strict settings of some of our users. All installed headers have to follow these rules:

- No C style casts (`-Wold-style-cast`)
- Use static_cast, const_cast or reinterpret_cast
- for basic types, use the constructor form: int(a) instead of (int)a
- See chapter "Casting" for more info

- No float comparisons (`-Wfloat-equal`)
- Use qFuzzyCompare to compare values with a delta
- Use qIsNull to check whether a float is binary 0, instead of comparing it to 0.0. 

- Don't hide virtual methods in subclasses (`-Woverloaded-virtual`)

- If the baseclass `A` has a `virtual int val()` and subclass `B` an overload with the same name, `int val(int x)`, `A`'s `val` function is hidden. Use the `using` keyword to make it visible again:

```cpp
class B: public A
{
    using A::val;
    int val(int x);
};
```

- Don't shadow variables (`-Wshadow`)
- avoid things like `this->x = x;`
- don't give variables the same name as functions declared in your class
- Always check whether a preprocessor variable is defined before probing its value (`-Wundef`)

```cpp
#if Foo == 0  // W R O N G
#if defined(Foo) && (Foo == 0) // Right
#if Foo - 0 == 0 // Clever, are we? Use the one above instead, for better readability
```

## Conventions for C++11 usage

**Note:** This section is not an accepted convention yet. This section serves as baseline for further discussions.

### Lambdas 

You can use lambdas with the following restrictions:

- You have to explicitly specify the return type, if the lambda contains more than a single expression. Otherwise it does not compile with VS2010.

```cpp
[]() -> QString {
    Foo *foo = activeFoo();
    return foo ? foo->displayName() : QString();
});
```
**NOT**
```cpp
[]() {
    Foo *foo = activeFoo();
    return foo ? foo->displayName() : QString();
});
```

- If you use static functions from the class that the lambda is located in, you have to explicitly capture this. Otherwise it does not compile with g++ 4.7 and earlier.

```cpp
void Foo::something()
{
    ...
    [this]() { Foo::someStaticFunction(); }
    ...
}
```
**NOT**
```cpp
void Foo::something()
{
    ...
    []() { Foo::someStaticFunction(); }
    ...
}
```

Format the lambda according to the following rules:

- Always write parentheses for the parameter list, even if the function does not take parameters.

```cpp
[]() { doSomething(); }
```
**NOT**
```cpp
[] { doSomething(); }
```

- Place the capture-list, parameter list, return type, and opening brace on the first line, the body indented on the following lines, and the closing brace on a new line.

```cpp
[]() -> bool {
    something();
    return isSomethingElse();
}
```
**NOT**
```cpp
[]() -> bool { something();
    somethingElse(); }
```

- Place a closing parenthesis and semicolon of an enclosing function call on the same line as the closing brace of the lambda.

```cpp
foo([]() {
    something();
});
```

- If you are using a lambda in an 'if' statement, start the lambda on a new line, to avoid confusion between the opening brace for the lambda and the opening brace for the 'if' statement.

```cpp
if (anyOf(fooList,
          [](Foo foo) {
              return foo.isGreat();
          }) {
    return;
}
```
**NOT**
```cpp
if (anyOf(fooList, [](Foo foo) {
          return foo.isGreat();
       }) {
    return;
}
```

- Optionally, place the lambda completely on one line if it fits.

```cpp
foo([]() { return true; });

if (foo([]() { return true; })) {
    ...
}
```

### auto Keyword

Optionally, you can use the auto keyword in the following cases. If in doubt, for example if using auto could make the code less readable, do not use auto. Keep in mind that code is read much more often than written.

- When it avoids repetition of a type in the same statement.

```cpp
auto something = new MyCustomType;
auto keyEvent = static_cast<QKeyEvent *>(event);
auto myList = QStringList() << QLatin1String("FooThing") << QLatin1String("BarThing");
```

- When assigning iterator types.

```cpp
auto it = myList.const_iterator();
```
