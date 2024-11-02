# Introduction

In this guide we deal with building the Liri projects from git.
Check in the [Contributor Guide](contributor-guide/index.md) how to get sources from the git repositories.

# Dependencies

We need:

* Git (>= 1.6.x)
* A working C++ compiler
* Qt (>= 6.6)
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
* cmake (>= 3.10)

## Install dependencies

Build essentials:

| Distro     | Command                                             |
| ---------- | --------------------------------------------------- |
| Debian     | `sudo apt-get install -y build-essential clang git` |
| Arch Linux | `sudo pacman -Syu base-devel git`                   |
| Fedora     | `sudo dnf install -y clang git`                     |
| OpenSUSE   | `sudo zypper install clang git-core`                |

Qt and cmake:

| Distro     | Command                                                                                                                                                                                                                                        |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Debian     | `sudo apt-get install -y qt6-default qtquickcontrols2-6-dev qml-module-qtwayland-compositor qtwayland6-dev-tools qtwebengine6-dev-tools qtwebengine6-private-dev qtwebengine6-dev qtmultimedia6-dev qtsvg6 qt6-doc qttools6-dev cmake ninja`   |
| Arch Linux | `sudo pacman -Syu qt6-quickcontrols2 qt6-wayland qt6-webengine qt6-multimedia qt6-svg qt6-doc qt6-tools qt6-5compat cmake ninja`                                                                                                               |
| Fedora     | `sudo dnf install -y qt6-qtbase-static qt6-qtbase-private-devel qt6-qtquickcontrols2-devel qt6-qtwayland-devel qt6-qtmultimedia-devel qt6-qtwebengine-devel qt6-qtsvg-devel qt6-doc-devel qt6-qttools-devel qt6-qt5compat-devel cmake ninja`   |
| OpenSUSE   | `sudo zypper install libqt6-qtquickcontrols2 libqt6-qtquickwayland-devel libqt6-qtwayland-private-headers-devel libqt6-qtwebengine-devel libqt6-qtmultimedia-devel libqt6-qtsvg-devel libqt6-qtdoc-devel libqt6-qttools-devel cmake ninja`     |

Other dependencies:

| Distro     | Command                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Debian     | `sudo apt-get install -y extra-cmake-modules libwayland-dev wayland-protocols libpam0g-dev libpolkit-qt6-1-dev libpolkit-gobject-1-dev libkf6solid-dev libsystemd-dev libdrm-dev libgbm-dev libinput-dev libxcb-cursor-dev libxcursor-dev libpulse-dev libkf6networkmanagerqt-dev libmodemmanagerqt-dev libglib2.0-dev dconf-service dconf-cli dconf-gsettings-backend dconf-tools libpipewire-0.2-dev gstreamer1.0-pipewire libxkbcommon-dev libqtgstreamer-dev libflatpak-dev libappstreamqt-dev` |
| Arch Linux | `sudo pacman -Syu extra-cmake-modules wayland pam polkit-qt6 solid libdrm libinput xcb-util-cursor pipewire-pulse networkmanager-qt modemmanager-qt glib2 dconf pipewire libxkbcommon flatpak appstream-qt libxcvt libdisplay-info`                                                      |
| Fedora     | `sudo dnf install -y extra-cmake-modules wayland-devel wayland-protocols-devel pam-devel polkit-devel polkit-qt6-1-devel kf6-solid-devel systemd-devel libdrm-devel mesa-libgbm-devel libinput-devel xcb-util-cursor-devel libXcursor-devel pulseaudio-libs-devel NetworkManager-libnm-devel ModemManager-glib-devel kf6-networkmanager-qt-devel kf6-modemmanager-qt-devel glib2-devel dconf pipewire-devel pipewire-utils libxkbcommon-devel flatpak-devel appstream-qt-devel gstreamer1-devel libxcvt-devel libdisplay-info-devel` |
| OpenSUSE   | `sudo zypper install extra-cmake-modules wayland-devel wayland-protocols pam-devel polkit-devel libpolkit-qt6-1-devel solid-devel systemd-devel libdrm-devel libgbm-devel libinput-devel xcb-util-cursor-devel libXcursor-devel pulseaudio-libs-devel libKF6NetworkManagerQt-devel libKF6ModemManagerQt-devel glib-devel dconf gsettings-backend-dconf pipewire-devel pipewire-tools gstreamer-plugin-pipewire libxkbcommon-devel flatpak-devel libAppstreamQt-devel`                               |

Marginal dependencies (used on unit tests, etc...):

| Distro     | Command                                          |
| ---------- | ------------------------------------------------ |
| Debian     | `sudo apt-get install -y umockdev-dev`           |
| Arch Linux | `sudo pacman -Syu umockdev`                      |
| Fedora     | `sudo dnf install -y umockdev-devel`             |
| OpenSUSE   | `sudo zypper install umockdev-devel`             |

# Build

Developers should open `CMakeLists.txt` with QtCreator and build there, that is way easier.

However if you know bash-fu it's possibile to build from sources.

We assume you have a terminal open in the sources root directory, where there's `CMakeLists.txt`.

Build:

```
mkdir .build
cd .build
cmake -DCMAKE_INSTALL_PREFIX=$(pwd)/install-root ..
make
make install
```
