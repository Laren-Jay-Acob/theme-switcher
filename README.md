# Dynamic Theme Switcher with Pywal

A complete configuration setup for dynamic theming in i3wm using pywal. This setup automatically generates and applies color schemes based on your wallpaper across multiple applications.

## 📋 Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
  - [1. Pywal Setup](#1-pywal-setup)
  - [2. i3wm Configuration](#2-i3wm-configuration)
  - [3. Alacritty Configuration](#3-alacritty-configuration)
  - [4. Rofi Configuration](#4-rofi-configuration)
  - [5. Wallpaper Switcher Script](#5-wallpaper-switcher-script)
- [Usage](#usage)
- [Troubleshooting](#troubleshooting)
- [Credits](#credits)

---

## Introduction

This configuration creates a unified, dynamic theming system that:
- Extracts colors from your wallpaper using **pywal**
- Applies those colors to **i3wm** window borders
- Themes your **Alacritty** terminal
- Styles your **Rofi** application launcher
- Provides a visual wallpaper selector with **Rofi**
- Persists colors across system reboots

Everything updates automatically when you change your wallpaper!

---

## Features

✨ **Dynamic Color Generation** - Colors extracted from wallpapers using pywal  
🎨 **Unified Theme** - Consistent colors across all applications  
🖼️ **Visual Wallpaper Selector** - Rofi-based wallpaper picker with image previews  
💾 **Persistent** - Theme persists across system reboots  
⚡ **Automatic Updates** - All apps reload with new colors instantly  

---

## Prerequisites

### Required Tools

```bash
# Arch Linux / Manjaro
sudo pacman -S i3-wm rofi feh alacritty python-pywal

# Ubuntu / Debian
sudo apt install i3 rofi feh alacritty
pip install pywal

# Fedora
sudo dnf install i3 rofi feh alacritty
pip install pywal
```

### Optional but Recommended

- [adi1090x's Rofi Themes](https://github.com/adi1090x/rofi) - Beautiful rofi themes
- A Nerd Font (for icons in rofi)

---

## Installation

### 1. Clone this repository

```bash
git clone https://github.com/YOUR-USERNAME/YOUR-REPO.git ~/dotfiles
cd ~/dotfiles
```

### 2. Backup your existing configs

```bash
cp ~/.config/i3/config ~/.config/i3/config.backup
cp ~/.config/alacritty/alacritty.toml ~/.config/alacritty/alacritty.toml.backup
cp -r ~/.config/rofi ~/.config/rofi.backup
```

### 3. Create necessary directories

```bash
mkdir -p ~/.config/wal/templates
mkdir -p ~/.config/rofi/colors
mkdir -p ~/Pictures/Wallpaper
mkdir -p ~/scripts
```

---

## Configuration

### 1. Pywal Setup

Pywal uses templates to generate color files for different applications.

#### Create Alacritty Template

Create `~/.config/wal/templates/alacritty.toml`:

```toml
[colors.primary]
background = '{background}'
foreground = '{foreground}'

[colors.cursor]
text = '{background}'
cursor = '{foreground}'

[colors.normal]
black = '{color0}'
red = '{color1}'
green = '{color2}'
yellow = '{color3}'
blue = '{color4}'
magenta = '{color5}'
cyan = '{color6}'
white = '{color7}'

[colors.bright]
black = '{color8}'
red = '{color9}'
green = '{color10}'
yellow = '{color11}'
blue = '{color12}'
magenta = '{color13}'
cyan = '{color14}'
white = '{color15}'
```

#### Create Rofi Template

Create `~/.config/wal/templates/colors-rofi-pywal.rasi`:

```rasi
/**
 * Pywal colors for rofi
 * Auto-generated from wallpaper
 **/

* {{
    background:     {background};
    background-alt: {color0};
    foreground:     {foreground};
    selected:       {color1};
    active:         {color2};
    urgent:         {color3};
}}
```

---

### 2. i3wm Configuration

Add the following to your `~/.config/i3/config`:

```config
# ===== Pywal Color Integration =====

# Restore pywal colors on startup
exec_always --no-startup-id wal -R

# Restore wallpaper
exec_always --no-startup-id ~/.fehbg

# Read colors from pywal
set_from_resource $bg           i3wm.color0 #000000
set_from_resource $fg           i3wm.color7 #ffffff
set_from_resource $color1       i3wm.color1 #ff0000
set_from_resource $color8       i3wm.color8 #888888

# Apply colors to i3 borders
# class                 border    backgr.   text  indicator child_border
client.focused          $color1   $color1   $fg   $color1   $color1
client.unfocused        $color8   $bg       $fg   $bg       $bg
client.focused_inactive $color8   $bg       $fg   $bg       $bg
client.urgent           $color1   $color1   $fg   $color1   $color1

# ===== Keybindings =====

# Launch wallpaper switcher
bindsym $mod+w exec --no-startup-id ~/scripts/wallpaper-switcher.sh

# You can add your other i3 config below...
```

**Note:** Replace `$mod` with your modifier key (usually `Mod4` for Super/Windows key)

---

### 3. Alacritty Configuration

Edit `~/.config/alacritty/alacritty.toml` and add this at the **top**:

```toml
# ===== Pywal Color Integration =====
[general]
import = ["~/.cache/wal/alacritty.toml"]

# Your other alacritty settings below...
```

#### Optional: Adjust foreground if too dark

If text is too dark, you can override after the import:

```toml
[general]
import = ["~/.cache/wal/alacritty.toml"]

# Override if foreground is too dark
[colors.primary]
foreground = '#ffffff'  # Force white text
```

---

### 4. Rofi Configuration

#### Step 1: Install adi1090x Rofi Themes (Optional but Recommended)

```bash
git clone --depth=1 https://github.com/adi1090x/rofi.git
cd rofi
chmod +x setup.sh
./setup.sh
```

#### Step 2: Create Pywal Color File

Create `~/.config/rofi/colors/pywal.rasi`:

```rasi
/**
 * Pywal colors for rofi
 * Imports from pywal cache
 **/

@import "~/.cache/wal/colors-rofi-pywal.rasi"
```

#### Step 3: Configure Your Rofi Theme

Edit your chosen rofi theme style file (e.g., `~/.config/rofi/launchers/type-3/style-9.rasi`)

Find the line:
```rasi
@import "colors.rasi"
```

Change it to:
```rasi
@import "~/.config/rofi/colors/pywal.rasi"
```

---

### 5. Wallpaper Switcher Script

Copy the `wallpaper-switcher.sh` script from this repository to `~/scripts/`:

```bash
cp wallpaper-switcher.sh ~/scripts/
chmod +x ~/scripts/wallpaper-switcher.sh
```

**Note:** Make sure to update the `THEME` variable in the script to match your rofi theme path.

---

## Usage

### Changing Your Theme

1. Press `Super + W` (or your configured keybinding)
2. Select a wallpaper from the rofi menu
3. Watch everything update automatically! 🎨

### Manual Theme Change

```bash
# Change wallpaper and generate colors
wal -i ~/Pictures/Wallpaper/your-image.jpg

# Reload i3
i3-msg reload

# Reload Alacritty
touch ~/.config/alacritty/alacritty.toml
```

### Adding New Wallpapers

Simply copy your wallpapers to `~/Pictures/Wallpaper/`:

```bash
cp /path/to/wallpaper.jpg ~/Pictures/Wallpaper/
```

They'll appear in the switcher automatically!

---

## Troubleshooting

<details>
<summary>Click to expand troubleshooting guide</summary>

### Colors too dark in terminal

Try different pywal backends for better contrast:

```bash
# In wallpaper-switcher.sh, change the wal line to:
wal -i "$WALLPAPER_DIR/$selected_name" -n --backend wal

# Or try haishoku for more vibrant colors:
wal -i "$WALLPAPER_DIR/$selected_name" -n --backend haishoku
```

### i3 reload has a delay

This is normal behavior. i3 reloads the entire configuration which takes 0.5-2 seconds. You can add a notification before reload to make it feel more responsive:

```bash
notify-send "Applying theme..." "Please wait"
i3-msg reload
notify-send "Theme Updated" "Colors applied!"
```

### Rofi theme errors

Make sure you're using the correct path to your rofi theme in the script:

```bash
# List available themes
ls ~/.config/rofi/launchers/
```

### Colors don't persist after reboot

Ensure `wal -R` is in your i3 config:

```config
exec_always --no-startup-id wal -R
```

### Alacritty import warning

If you see "import has been deprecated", use:

```toml
[general]
import = ["~/.cache/wal/alacritty.toml"]
```

Instead of:

```toml
import = ["~/.cache/wal/alacritty.toml"]
```

### Rofi doesn't show image previews

Make sure you have a recent version of rofi (1.7.0+) with image support enabled.

### Wallpaper doesn't persist after reboot

The `~/.fehbg` script is automatically created by feh. Make sure you have this in your i3 config:

```config
exec_always --no-startup-id ~/.fehbg
```

</details>

---

## File Structure

```
~/
├── .config/
│   ├── i3/
│   │   └── config                          # i3 configuration
│   ├── alacritty/
│   │   └── alacritty.toml                  # Alacritty configuration
│   ├── rofi/
│   │   ├── colors/
│   │   │   └── pywal.rasi                  # Pywal color importer
│   │   └── launchers/
│   │       └── type-3/
│   │           └── style-9.rasi            # Your rofi theme
│   └── wal/
│       └── templates/
│           ├── alacritty.toml              # Alacritty color template
│           └── colors-rofi-pywal.rasi      # Rofi color template
├── scripts/
│   └── wallpaper-switcher.sh               # Theme switcher script
└── Pictures/
    └── Wallpaper/                          # Your wallpapers here
```

---

## Configuration Options

### Changing the Wallpaper Directory

Edit `wallpaper-switcher.sh`:

```bash
WALLPAPER_DIR="$HOME/your/custom/path"
```

### Using a Different Rofi Theme

Edit `wallpaper-switcher.sh`:

```bash
THEME="$HOME/.config/rofi/launchers/type-1/style-5.rasi"
```

### Customizing i3 Colors

You can use different pywal colors for i3 borders. Available colors:

- `i3wm.color0` through `i3wm.color15` - pywal's 16 colors
- `i3wm.background` - background color
- `i3wm.foreground` - foreground color

Example:

```config
set_from_resource $focused     i3wm.color4 #0000ff  # Use blue
set_from_resource $unfocused   i3wm.color8 #888888  # Use gray
```

---

## Credits

- **Pywal** - [dylanaraps/pywal](https://github.com/dylanaraps/pywal)
- **Rofi Themes** - [adi1090x/rofi](https://github.com/adi1090x/rofi)
- **i3wm** - [i3wm.org](https://i3wm.org/)
- **Alacritty** - [alacritty.org](https://alacritty.org/)

---

## License

MIT License - Feel free to use and modify!

---

## Screenshots

