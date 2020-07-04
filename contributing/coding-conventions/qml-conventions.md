# QML Coding Conventions

This document contains the QML coding conventions that all Liri projects follow.

## QML Object Declarations

Throughout our code, documentation and examples, QML object attributes are always structured in the following order:

- id
- property declarations
- signal declarations
- JavaScript functions
- object properties
- child objects
- states
- transitions

For better readability, we separate these different parts with an empty line.

For example, a hypothetical photo QML object would look like this:

```qml
Rectangle {
    id: photo                                               // id on the first line makes it easy to find an object

    property bool thumbnail: false                          // property declarations
    property alias image: photoImage.source

    signal clicked()                                        // signal declarations

    function doSomething(x)                                 // javascript functions
    {
        return x + photoImage.width;
    }

    color: "gray"                                           // object properties
    x: 20; y: 20; height: 150                               // try to group related properties together
    width: {                                                // large bindings
        if (photoImage.width > 200)
            photoImage.width;
        else
            200;
    }

    Rectangle {                                             // child objects
        id: border
        anchors.centerIn: parent; color: "white"

        Image { id: photoImage; anchors.centerIn: parent }
    }

    states: State {                                         // states
        name: "selected"
        PropertyChanges { target: border; color: "red" }
    }

    transitions: Transition {                               // transitions
        from: ""; to: "selected"
        ColorAnimation { target: border; duration: 200 }
    }
}
```

## Grouped Properties

If using multiple properties from a group of properties, consider using group notation instead of dot notation if it improves readability.

For example, this:

```qml
Rectangle {
    anchors.left: parent.left; anchors.top: parent.top; anchors.right: parent.right; anchors.leftMargin: 20
}

Text {
    text: "hello"
    font.bold: true; font.italic: true; font.pixelSize: 20; font.capitalization: Font.AllUppercase
}
```

could be written like this:

```qml
Rectangle {
    anchors { left: parent.left; top: parent.top; right: parent.right; leftMargin: 20 }
}

Text {
    text: "hello"
    font { bold: true; italic: true; pixelSize: 20; capitalization: Font.AllUppercase }
}
```

## Lists

If a list contains only one element, we generally omit the square brackets.

For example, it is very common for a component to only have one state.

In this case, instead of:

```qml
states: [
    State {
        name: "open"
        PropertyChanges { target: container; width: 200 }
    }
]
```

we will write this:

```qml
states: State {
    name: "open"
    PropertyChanges { target: container; width: 200 }
}
```

## JavaScript Code

If the script is a single expression, we recommend writing it inline:

```qml
Rectangle { color: "blue"; width: parent.width / 3 }
```

If the script is only a couple of lines long, we generally use a block:

```qml
Rectangle {
    color: "blue"
    width: {
        var w = parent.width / 3;
        console.debug(w);
        return w;
    }
}
```

If the script is more than a couple of lines long or can be used by different objects, we recommend creating a function and calling it like this:

```qml
function calculateWidth(object)
{
    var w = object.width / 3;
    // ...
    // more javascript code
    // ...
    console.debug(w);
    return w;
}

Rectangle { color: "blue"; width: calculateWidth(parent) }
```

For long scripts, we will put the functions in their own JavaScript file and import it like this:

```qml
import "myscript.js" as Script

Rectangle { color: "blue"; width: Script.calculateWidth(parent) }
```

If the code is longer than one line and hence within a block, we use semicolons to indicate the end of each statement:

```qml
MouseArea {
    anchors.fill: parent
    onClicked: {
        var scenePos = mapToItem(null, mouseX, mouseY);
        console.log("MouseArea was clicked at scene pos " + scenePos);
    }
}
```

This document is based on [Qt's QML Coding Conventions](http://doc.qt.io/qt-5/qml-codingconventions.html) which is (C) 2017 The Qt Company Ltd, licensed under the terms of the [GNU Free Documentation License version 1.3](http://www.gnu.org/licenses/fdl.html) as published by the Free Software Foundation.
