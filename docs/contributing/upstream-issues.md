# Upstream Issues

This is a living document that lists issues from upstream projects that introduce problems to Liri OS and apps and whose resolution is not a Liri responsability.

## Wayland

Wayland needs to address some issues before claiming feature parity with Xorg.

* A client cannot change the keymap. This would be required to let Calamares change the keyboard layout, otherwise the user won't be able to test the keyboard layout during the installation.
* A compositor cannot change the cursor theme on all clients. We can craft a solution only for Qt clients, but it won't work for other toolkits. This means that changing the cursor theme from Settings requires to restart the desktop.

## XWayland

Clients are blurry on HiDPI. See this IRC conversation:

```md
<plfiorini> xwayland clients seem blurry on hidpi, compared to their wayland counterpart. doesn't seem to be a toolkit issue since this happens with both gtk and qt. is this a known issue?
<SardemFF7> plfiorini: short answer: yes, nothing can be done about it
<plfiorini> SardemFF7, so nothing i could fix with the compositor author hat on?
<SardemFF7> the long answer is that you could, maybe, fix some of the clients by providing more information, or by lying, but that may break other clients, so you’d probably need special extra support in X11 toolkits/apps to make it work, and they probably have Wayland support anyway
<fredrikh> a different scaling filter could help
<SardemFF7> that one is up to the compositor
<SardemFF7> but I don’t think it’d fix everything magically
<fredrikh> nope
<fredrikh> still, bilnear is probably the worst magnification filter if you don't want to things to look blurry
<plfiorini> thanks for your answer
<SardemFF7> plfiorini: there is a thread on the ML about that, btw
<SardemFF7> plfiorini: https://lists.freedesktop.org/archives/wayland-devel/2017-September/034957.html
```

## QtDeclarative

* TextEdit doesn't correctly update formats set by QSyntaxHighlighter: [QTBUG-58092](https://bugreports.qt.io/browse/QTBUG-58092)
* ListView with transitions does not always position delegates properly: [QTBUG-62148](https://bugreports.qt.io/browse/QTBUG-62148)

## QtWayland

Qt 5.8.0 introduces QtWayland as an official module, finally the QtWaylandCompositor framework is considered stable.  However it still lack a few features or has bugs that need to be fixed:

* Partial subsurface support: missing raise, lower and synchronization.
* D&D works only on one output: [QTBUG-55584](https://bugreports.qt.io/browse/QTBUG-55584)
* No support for animated mouse cursors: [QTBUG-48181](http://bugreports.qt.io/browse/QTBUG-48181)
* Dropdown window closes on key press: [QTBUG-55403](https://bugreports.qt.io/browse/QTBUG-55403)
* Compose key support missing in QtWayland: [QTBUG-54792](https://bugreports.qt.io/browse/QTBUG-54792) **FIXED IN 5.11.0**
* Key repeat settings ignored: [QTBUG-55615](https://bugreports.qt.io/browse/QTBUG-55615)
* [Wayland] Modifiers not working in wheelEvent: [QTBUG-61488](https://bugreports.qt.io/browse/QTBUG-61488)

## QtQuick Controls 2

* Roboto doesn't support all languages, should fallback to Noto: [QTBUG-59579](https://bugreports.qt.io/browse/QTBUG-59579)
* Dialog should respond to Enter key: [QTBUG-60361](https://bugreports.qt.io/browse/QTBUG-60361)
* MenuItem is not triggered by the "Enter" key: [QTBUG-67275](https://bugreports.qt.io/browse/QTBUG-67275)

## QtWebEngine

* Crashes on Wayland, means that Liri Browser doesn't work on Wayland: [QTBUG-55384](https://bugreports.qt.io/browse/QTBUG-55384)

## Solid

* udisks2 backend makes blocking dbus calls: [380990](https://bugs.kde.org/show_bug.cgi?id=380990)

## Gtk+

While Liri OS is focused on Qt applications, we still want compatibility with Gtk+ applications.

* Bad app_id for Gtk+ apps: [769641](https://bugzilla.gnome.org/show_bug.cgi?id=769641)

## Snap Online Services

* [build.snapcraft.io](https://build.snapcraft.io/) doesn't respect publishing rights for shared snaps: [#799](https://github.com/canonical-websites/build.snapcraft.io/issues/799)
* Proxy token lifetime for launchpad-buildd is limited [1709678](https://bugs.launchpad.net/rutabaga/+bug/1709678)

## Snapcraft

* `qmake` plugin doesn't support choosing a custom Qt version: [1670146](https://bugs.launchpad.net/snapcraft/+bug/1670146)
