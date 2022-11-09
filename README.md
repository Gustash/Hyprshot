# Hyprshot

Hyprshot is an utility to easily take screenshot in Hyprland using your mouse.

It allows taking screenshots of windows, regions and monitors which are saved to a folder of your choosing and copied to your clipboard.

## Installation

## Dependencies

- hyprland (this one should be obvious)
- grim     (to take the screenshot)
- slurp    (to select what to screenshot)
- wl-copy  (to copy screenshot to clipboard)

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

You can choose which directory Hyprshot will save screenshots in by setting an `HYPRSHOT_DIR` environment variable to your preferred location.

If `HYPRSHOT_DIR` is not set, Hyprshot will attempt to save to `XDG_PICTURES_DIR` and will further fallback to your home directory if this is also not available.
