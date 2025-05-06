# Hyprshot

[![AUR version](https://img.shields.io/aur/version/hyprshot?label=hyprshot&logo=arch+linux)](https://aur.archlinux.org/packages/hyprshot)
[![AUR git version](https://img.shields.io/aur/version/hyprshot-git?label=hyprshot-git&logo=arch+linux)](https://aur.archlinux.org/packages/hyprshot-git)
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/Gustash/hyprshot?color=green&logo=github)](https://github.com/Gustash/hyprshot/releases/latest)

Hyprshot is an utility to easily take screenshot in Hyprland using your mouse.

It allows taking screenshots of windows, regions and monitors which are saved to a folder of your choosing and copied to your clipboard.

## Installation

### ALT Sisyphus

```shell
# apt-get install hyprshot
```

### Arch Linux

You can install the [hyprshot](https://aur.archlinux.org/packages/hyprshot) package in AUR.

### Gentoo Linux

Activate wayland overlay as described in [README](https://github.com/bsd-ac/wayland-desktop#activate-overlay-via-eselect-repository), allow **~amd64** keyword and then install it:

```bash
# emerge --ask gui-apps/hyprshot
```

### Dependencies

- hyprland (this one should be obvious)
- jq (to parse and manipulate json)
- grim or wayshot (to take the screenshot)
- slurp (to select what to screenshot)
- wl-clipboard (to copy screenshot to clipboard)
- libnotify (to get notified when a screenshot is saved)

### Optional Dependencies

- hyprpicker (to freeze the screen contents with the `--freeze` flag)
- satty or swappy (to pass the capture to be annotated on `-a, --annotate`)

### Manual

To install manually, simply clone this repo and copy/symlink the `hyprshot` script to a folder in your `PATH`:

```bash
$ git clone https://github.com/Gustash/hyprshot.git Hyprshot
$ ln -s $(pwd)/Hyprshot/hyprshot $HOME/.local/bin
$ chmod +x Hyprshot/hyprshot
```

## Usage

You can get help on how to use hyprshot by executing:

```bash
$ hyprshot -h
```

The simplest usage of Hyprshot is executing it with one of the available modes.

For example, to screenshot an open window:

```bash
$ hyprshot -m window
```

You can also skip saving the screenshot to a file, copying it only to the clipboard:

```bash
$ hyprshot -m output --clipboard-only
```

## Configuration

You can add the various modes as keybindings in your Hyprland config like so:

```
# ~/.config/hypr/hyprland.conf

...

# Screenshot a window
bind = $mainMod, PRINT, exec, hyprshot -m window
# Screenshot a monitor
bind = , PRINT, exec, hyprshot -m output
# Screenshot a region
bind = $shiftMod, PRINT, exec, hyprshot -m region
```

This would allow you to:

Take a screenshot of a window by using `MOD + PrintScr`

Take a screenshot of a monitor by using `PrintScr`

Take a screenshot of a region by using `MOD + Shift + PrintScr`

## Save location

You can choose which directory Hyprshot will save screenshots in by setting an `HYPRSHOT_DIR` environment variable to your preferred location.

If `HYPRSHOT_DIR` is not set, Hyprshot will attempt to save to `XDG_PICTURES_DIR` and will further fallback to your home directory if this is also not available.
