## Modules

All modules revolve around [Fluid](https://github.com/lirios/fluid), a library that implements the [Material Design](https://material.io) guidelines
with QtQuick.  You can learn more about [here](https://liri.io/docs/sdk/fluid/develop/).

To summarize the area of interest we have:

- Cross-platform apps
- Core apps
- Libraries
- Desktop environment
- Operating system

### Cross-platform apps

Cross-platform apps are designed to run on all operating systems such as Linux, macOS, Windows and
mobile platforms like Android and iOS.

The cross-platform apps are:

- [Browser](https://github.com/lirios/browser) (Docs [here](browser/index.md))
- [Calculator](https://github.com/lirios/calculator)
- [Text](https://github.com/lirios/text) (Docs [here](text/index.md))

### Core apps

Core apps are primarily designed with Linux in mind and, in some cases, might even be useful only on LiriOS.

The core apps are:

- [AppCenter](https://github.com/lirios/appcenter)
- [Files](https://github.com/lirios/files)
- [Terminal](https://github.com/lirios/terminal)

### Libraries

We also develop the following libraries:

- [Fluid](https://github.com/lirios/fluid)
- [QtAccountsService](https://github.com/lirios/qtaccountsservice)
- [QtGSettings](https://github.com/lirios/qtgsettings)
- [QbsShared](https://github.com/lirios/qbs-shared)
- [LibLiri](https://github.com/lirios/libliri)
- [Vibe](https://github.com/lirios/vibe)

### Desktop environment

The desktop environment is comprised of the following projects, all with the same release cycle.

- [Shell](https://github.com/lirios/shell)
- [Liri Wayland](https://github.com/lirios/wayland)
- [Workspace](https://github.com/lirios/workspace)
- [Settings](https://github.com/lirios/settings)
- [QPA Theme](https://github.com/lirios/platformtheme)
- [Themes](https://github.com/lirios/themes)

### Operating system

- [Calamares Branding](https://github.com/lirios/calamares-branding)

## Vibe

Vibe contains QML modules that are mostly used by the shell with dependencies to
a lot of libraries such as networkmanager-qt, modemmanager-qt, policy kit, pulseaudio etc...
It also has a couple of libraries that are needed by most core apps such as VibeCore and VibeGSettings.

The idea is to move VibeCore to a repository called libliri and split the library into:

- Core
- DBusService
- Notifications

VibeGSettings would go into its own repository called qtgsettings.

## Workspace

The workspace repository contains desktop menu, GSettings schemas, Qt platform theme plugin, screenshot and screencast, with the most notable dependency being QtGStreamer.

The Qt platform theme plugin really need to be moved to its own repository and depend only on qtgsettings.
At this point the workspace repository would be left with a few things, so desktop menu and GSettings schemas could go into liri-shell and the screenshot and screencast utilities in their repository.
