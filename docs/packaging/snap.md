## Snap

Our snap packages are built with [snapcraft](https://snapcraft.io/).

If you are new to snapcraft, we highly recommend you to check out their [introduction to building snaps](https://docs.snapcraft.io/build-snaps/)

There are a few repositories containing sources and packaging data relevant for building our snap packages.

### Platform snap

Our [platform snap](https://github.com/lirios/platform-snap) serves as a bundle for libraries commonly used by our projects. It contains Qt, KDE dependencies, Fluid and some other pieces.

Right now it has to be build on Ubuntu 16.04 because this is the current base for snap applications. 
Even if you are running Ubuntu 16.04 however, you should use `snapcraft cleanbuild` or create your own [LXC container](https://linuxcontainers.org/) to build the platform snap in.

By doing so, you make sure you don't pollute your system with libraries installed for the snap and you also make sure the snap will not be polluted by your system.

Be warned that it will download and build a big chunk of its contents from source so the build might (depending on your machine) take several hours and will use multiple gigabytes of hard disk space.

#### Naming and versioning

Projects depending on the platform snap connect to it using the content interface. 
Unfortunately, the only way to handle compatibility breaks right now is to publish a new platform snap with a new name.
Because of the way snap is designed, there is currently no way to have more than one version of a snap package installed.
Therefore there is no way for a snap to pin a given version of another snap (like the platform snap) as that would require the ability to have mutliple versions installed.

The platform snap naming roughly follows the Liri OS versioning and looks like the following:

For a Liri OS version following semver `<major>.<minor>.<patch>`, the platform snap naming equals `liri-platform-<major>-<minor>[-<release build>]`.
`<release build>` is an incremental number to handle incompatible changes between Liri OS releases.

For example, consider the following scenario:

Liri OS 1.0.0 gets released. `liri-platform-1-0` gets released. 
An incompatible change in one of the included packages happens which would affect projects depending on the platform snap.
As Liri OS 1.1.0 is not released yet, `liri-platform-1-0-1` is being created.
Once Liri OS 1.1.0 is out, `liri-platform-1-1` will be created.

### Snapcraft parts

To deduplicate snapcraft configuration, there is a set of [snapcraft parts](https://github.com/lirios/snapcraft-parts) available.
They can be used in snap applications using the `after` keywoard, e.g. `after: [liri-platform-<version>]`, where `<version>` is the current platform snap version.

Snapcraft parts contain a launcher to manage the connection with the platform snap and setup the runtime environment as well as other assets like a `qt.conf` and workarounds for upstream issues.

See the [readme in the GitHub repository](https://github.com/lirios/snapcraft-parts) for more information.

### Snapcraft plugins

[Snapcraft plugins](https://github.com/lirios/snapcraft-plugins) are used to share a common set of custom snapcraft plugins that are used by the projects depending on the platform snap.

The custom Qbs plugin for example sets up the build environment to make it possible to build packages against the platform snap package.

### Snap packages

The [snap packages](https://github.com/lirios/snap-packages) repository is where development on the snap packages for our projects takes place.
The packaging depends on the snapcraft plugins and parts mentioned above as well as the platform snap at build time.
