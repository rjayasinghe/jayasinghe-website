---
title: "Gnome - Y U No show Snap Apps?!"
date: 2020-05-27T13:36:50+02:00
draft: false
---

**This behavior has been fixed in the latest version of Ubuntu 20.04. The described fix is not needed anymore.**

After a short affair with Fedora 31 I recently switched to Ubuntu 20.04 LTS. Since I cannot stand the purple color and the overall look and feel of the Ubuntu Gnome Theme I switched to the Vanilla Gnome desktop shortly after install:

```bash
sudo apt install vanilla-gnome-desktop
```

I use Gnome Software to install my applications. Some of the apps that I need (Mattermost, Riot, Signal, ...) are more or less transparently installed as snap bundles. Everything worked fine with the Ubuntu Desktop. But after I switched to the Gnome Desktop I could not find any of the snap applications in the app launcher.

After some research I realized that snap is creating the .desktop entries for all the installed apps but not at the place gnome is looking for them. A simple symlink fixed the problem:

```bash
ln -s /var/lib/snapd/desktop/applications/ ~/.local/share/applications/snap 
```

Fun fact: this problem only occured when I started Gnome with Wayland. Gnome on X11 was somehow able to find the .desktop entries where Snap did place them. Switching the display server should be totally transparent to any application discovery of the desktop environment. Gnome suffering from leaky abstraction?
