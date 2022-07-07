# Max's Guide to Maximum Efficiency

The official, Max approved record of answers to "How did I do that again?"


| Contents                                                                    |
|-----------------------------------------------------------------------------|
| 1 &nbsp; [Dropbox on Arch Linux](#dropbox-on-arch-linux)                    |
| &nbsp; 1.1 &nbsp; [Gnome 40 Display Manager](#gnome-40-display-manager)     |

## Dropbox on Arch Linux

### Gnome 40 Display Manager

First install the prerequisite python package

```bash
sudo pacman -S python-gpgme
```

Next, make sure you have the
[TopIconsFix](https://extensions.gnome.org/extension/1674/topiconsfix/)
Gnome extension installed.

Now install the `dropbox` AUR repository along with the command line client and
nautilus-integration

```bash
git clone https://aur.archlinux.org/dropbox.git
cd dropbox
makepkg -si
cd ..
git clone https://aur.archlinux.org/dropbox-cli.git 
cd dropbox-cli
makepkg -si
cd ..
git clone https://aur.archlinux.org/nautilus-dropbox.git
cd nautilus-dropbox
makepkg -si
cd ..
```

Note: You may get a GPG key error for `dropbox`, in which case you simply add
the key using

```bash
gpg --recv-key <KEY>
```

Before starting `dropbox`, edit the `systemd` client to default to the main
display

```bash
sudo systemctl edit dropbox@<USERNAME>

```

and add

```
[Service]
Environment=DISPLAY=:0
```

Now you can enable the service to start on startup and reboot

```bash
sudo systemctl enable dropbox@<USERNAME>
sudo reboot
```

### Prevent automatic updates

Since at least version 2.4.6 (see comments around 2013-11-06 on AUR), Dropbox has had an auto-update capability
which downloads a new binary to the `~/.dropbox-dist/` folder. The service then attempts to hand over control to
this binary and dies, causing systemd to re-start the service, generating a conflict and an endless loop of log-filling,
CPU-eating misery.

A workaround is to prevent Dropbox from downloading the automatic update by creating the
`~/.dropbox-dist/` folder and making it read-only:

```bash
rm -rf ~/.dropbox-dist
install -dm0 ~/.dropbox-dist
```

This appears to be necessary for modern Dropbox clients to operate successfully from systemd on arch. 
