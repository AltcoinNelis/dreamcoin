
Debian
====================
This directory contains files used to package dreamcoind/dreamcoin-qt
for Debian-based Linux systems. If you compile dreamcoind/dreamcoin-qt yourself, there are some useful files here.

## dreamcoin: URI support ##


dreamcoin-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install dreamcoin-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your dreamcoin-qt binary to `/usr/bin`
and the `../../share/pixmaps/dreamcoin128.png` to `/usr/share/pixmaps`

dreamcoin-qt.protocol (KDE)

