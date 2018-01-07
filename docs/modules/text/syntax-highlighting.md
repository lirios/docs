# Syntax Highlighting

This page describes Liri Text's syntax highlighting system. Please familiarize yourself with it's content before reporting bugs against syntax highlighting or making contributions to it's development.

Liri Text implements [GtkSourceView specification](https://developer.gnome.org/gtksourceview/stable/pt02.html) for language definitions with only exception: **dupnames attribute is not currently supported**.

If you find any other difference in highlighting behavior of GtkSourceView/gedit and Liri Text, please report it as a bug.

## Manually adding language definitions

You can manually add languages to your local installation of Liri Text. To do so, put language definition into one of the following directories.

### System directory

`INSTALL_PREFIX/share/liri-text/language-specs/` on Linux, where INSTALL_PREFIX is defined during build time and usually equals to /usr for system packages or /usr/local for manual builds.

`BUNDLE_PATH/Resources/language-specs/` on macOS.

`APP_DIRECTORY\language-specs\` on Windows.

### User directory

`USER_DATA/Liri/Text/language-specs/` where USER_DATA is OS-dependant.

## Contributing additional languages to Liri Text

We will gladly accept more languages into the base package. If you want to contribute one, please try to follow advices and conventions from GtkSourceView [stated here](https://developer.gnome.org/gtksourceview/stable/lang-reference.html).

Once you have a language definition you're ready to send to us, add it to data/language-specs directory of this repository and open a pull request.

## Reporting bugs

If you find a misbehavior in Liri Text's syntax highlighting, please check if it differs from the one of gedit before opening an issue. All prebundled languages should behave identically, third-party languages too as long as they don't have `dupnames` attribute set for any context or regex.

If the behavior is identical, but you think it's incorrect, you can still open an issue and we'll consider changing corresponding language definition.
