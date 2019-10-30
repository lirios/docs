# Introduction

In this guide we deal with building the Liri projects from git.
Check in the [Contributor Guide](contributor-guide/index.md) how to get sources from the git repositories.

# Dependencies

We need:

* Git (>= 1.6.x)
* A working C++ compiler
* Qt (>= 5.10)
  * qtbase
  * qtdoc
  * qtdeclarative
  * qtgraphicaleffects
  * qtquickcontrols2
  * qtsvg
  * qtwayland
  * qttools
  * qtmultimedia
  * qtwebengine
* qbs (>= 1.10)

## Install dependencies

Build essentials:

| Distro     | Command                                       |
| ---------- | --------------------------------------------- |
| Debian     | `sudo apt-get install -y build-essential git` |
| Arch Linux | `sudo pacman -Syu base-devel git`             |
| Fedora     | `sudo dnf install -y gcc-c++ git`             |
| OpenSUSE   | `sudo zypper install gcc-c++ git-core`        |

Qt and qbs:

| Distro     | Command                                                                                                                                                                                                                                        |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Debian     | `sudo apt-get install -y qt5-default qtquickcontrols2-5-dev qml-module-qtwayland-compositor qtwayland5-dev-tools qtwebengine5-dev-tools qtwebengine5-private-dev qtwebengine5-dev qtmultimedia5-dev qtsvg5 qt5-doc qttools5-dev qbs qtchooser` |
| Arch Linux | `sudo pacman -Syu qt5-quickcontrols2 qt5-wayland qt5-webengine qt5-multimedia qt5-svg qt5-doc qt5-tools qbs`                                                                                                                                   |
| Fedora     | `sudo dnf install -y qt5-qtbase-static qt5-qtquickcontrols2-devel qt5-qtwayland-devel qt5-qtmultimedia-devel qt5-qtwebengine-devel qt5-qtsvg-devel qt5-qtdoc qt5-qttools-devel qbs qtchooser`                                                  |
| OpenSUSE   | `sudo zypper install libqt5-qtquickcontrols2 libqt5-qtquickwayland-devel libqt5-qtwayland-private-headers-devel libqt5-qtwebengine-devel libqt5-qtmultimedia-devel libqt5-qtsvg-devel libqt5-qtdoc-devel libqt5-qttools-devel qbs`             |

Other dependencies:

| Distro     | Command                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Debian     | `sudo apt-get install -y libwayland-dev libqtxdg0-dev qtxdg-dev-tools libpam0g-dev libpolkit-qt5-1-dev libpolkit-gobject-1-dev libkf5solid-dev libsystemd-dev libdrm-dev libgbm-dev libinput-dev libxcb-cursor-dev libxcursor-dev libpulse-dev libkf5networkmanagerqt-dev libmodemmanagerqt-dev libglib2.0-dev dconf-service dconf-cli dconf-gsettings-backend dconf-tools libpipewire-0.2-dev gstreamer1.0-pipewire libxkbcommon-dev libqtgstreamer-dev libflatpak-dev libappstreamqt-dev` |
| Arch Linux | `sudo pacman -Syu wayland libqtxdg pam polkit-qt5 solid libdrm libinput xcb-util-cursor pulseaudio networkmanager-qt modemmanager-qt glib2 dconf pipewire libxkbcommon flatpak appstream-qt`                                                                                                                                                                                                                                                                                   |
| Fedora     | `sudo dnf install -y wayland-devel libqtxdg-devel pam-devel polkit-devel polkit-qt5-1-devel kf5-solid-devel systemd-devel libdrm-devel mesa-libgbm-devel libinput-devel xcb-util-cursor-devel pulseaudio-libs-devel NetworkManager-libnm-devel ModemManager-glib-devel kf5-networkmanager-qt-devel kf5-modemmanager-qt-devel glib2-devel dconf pipewire-devel pipewire-utils libxkbcommon-devel flatpak-devel appstream-qt-devel`                                        |
| OpenSUSE   | `sudo zypper install wayland-devel libqtxdg-devel pam-devel polkit-devel libpolkit-qt5-1-devel solid-devel systemd-devel libdrm-devel libgbm-devel libinput-devel xcb-util-cursor-devel pulseaudio-libs-devel libKF5NetworkManagerQt-devel libKF5ModemManagerQt-devel glib-devel dconf gsettings-backend-dconf pipewire-devel pipewire-tools gstreamer-plugin-pipewire libxkbcommon-devel libQtGStreamer-0_10-devel flatpak-devel libAppstreamQt-devel`                                     |

Marginal dependencies (used on unit tests, etc...):

| Distro     | Command                                          |
| ---------- | ------------------------------------------------ |
| Debian     | `sudo apt-get install -y umockdev-dev`           |
| Arch Linux | `sudo pacman -Syu umockdev`                      |
| Fedora     | `sudo dnf install -y umockdev-devel`             |
| OpenSUSE   | `sudo zypper install umockdev-devel`             |

# Build

Developers should open `liri.qbs` with QtCreator and build there, that is way easier.

However if you know bash-fu it's possibile to build from sources.

We assume you have a terminal open in the sources root directory, where there's `liri.qbs`.

First, let's set up `qbs` if you haven't already done it yet.

```
qbs setup-toolchains --type gcc /usr/bin/g++ gcc
qbs setup-qt $(which qmake) qt5
qbs config profiles.qt5.baseProfile gcc
```

Then start the build:

```
qbs -d build -j $(nproc) profile:qt5
```
