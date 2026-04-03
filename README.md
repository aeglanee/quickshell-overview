# Quickshell Overview for Hyprland

<div align="center">

A standalone workspace overview module for Hyprland using Quickshell - shows all workspaces with live window previews, drag-and-drop support, and Super+Tab keybind.

![Quickshell](https://img.shields.io/badge/Quickshell-0.2.0-blue?style=flat-square)
![Hyprland](https://img.shields.io/badge/Hyprland-Compatible-purple?style=flat-square)
![Qt6](https://img.shields.io/badge/Qt-6-green?style=flat-square)
![License](https://img.shields.io/badge/License-GPL-orange?style=flat-square)


</div>

---

## 📸 Preview

![Overview Screenshot](assets/image.png)

https://github.com/user-attachments/assets/9c3d2488-1c24-4cdd-84cd-87c4397d02a8

> *Workspace overview showing live window previews with drag-and-drop support*

---

## ✨ Features

- 🖼️ Visual workspace overview showing all workspaces and windows
- 🖥️ Multi-monitor support with proper scaling and vertical/rotated monitors [in experimental branch]
- 📐 Smart row hiding - optionally hide empty workspace rows
- 🎯 Click windows to focus them
- 🖱️ Middle-click windows to close them  
- 🔄 Drag and drop windows between workspaces
- ⌨️ Keyboard navigation (Arrow keys, vim keys, number shortcuts)
- 💡 Hover tooltips showing window information
- 🎨 Material Design 3 theming
- ⚡ Smooth animations and transitions

## 📦 Installation

### Arch Linux (AUR)

For Arch Linux users, you can install directly from the AUR:

```bash
# Using yay
yay -S quickshell-overview-git

# Using paru
paru -S quickshell-overview-git
```

On AUR installs, module files are package-managed under:

```text
/etc/xdg/quickshell/overview/
```

Put your custom settings in:

```text
~/.config/quickshell/overview/config.json
```

Then add the keybind and auto-start to your Hyprland config (see Setup steps 2-4 below).

### Prerequisites

- **Hyprland** compositor
- **Quickshell** ([installation guide](https://quickshell.org/docs/v0.1.0/guide/install-setup/))
- **Qt 6** with modules: QtQuick, QtQuick.Controls

### Setup

1. **Install module files** (choose one):
   - **Git clone (manual install):**
   ```bash
   git clone https://github.com/Shanu-Kumawat/quickshell-overview ~/.config/quickshell/overview
   ```
   - **AUR package:** use the command above (`yay -S quickshell-overview-git` or `paru -S ...`)

2. **Add keybind** to your Hyprland config (`~/.config/hypr/hyprland.conf`):
   ```conf
   bind = Super, TAB, exec, qs ipc -c overview call overview toggle
   ```

3. **Auto-start** the overview (add to Hyprland config):
   ```conf
   exec-once = qs -c overview
   ```

4. **Reload Hyprland**:
   ```bash
   hyprctl reload
   ```

### Manual Start (if needed)

```bash
qs -c overview &
```

### NixOS

For NixOS users, ensure Quickshell has access to required Qt6 modules:

```nix
# In your configuration.nix or home-manager config
environment.systemPackages = with pkgs; [
  quickshell
  qt6.qtwayland
];
```

If you're using home-manager:

```nix
home.packages = with pkgs; [
  quickshell
  qt6.qtwayland
];
```

## 🎮 Usage

| Action | Description |
|--------|-------------|
| **Super + Tab** | Toggle the overview |
| **Arrow Keys / h/l** | Navigate left/right within current row* |
| **Up/Down / j/k** | Navigate between workspace rows |
| **1-9, 0** | Jump to Nth workspace in current group (0 = 10th) |
| **Escape / Enter** | Close the overview |
| **Click workspace** | Switch to that workspace |
| **Click window** | Focus that window |
| **Middle-click window** | Close that window |
| **Drag window** | Move window to different workspace |

> *When `hideEmptyRows` is enabled, left/right navigation wraps within the current visible row for better UX

---

## ⚙️ Configuration

> **⚠️ Want to change size, position, workspace count, or toggles?**
> Create/edit `~/.config/quickshell/overview/config.json`.

`Config.qml` inside the module is now treated as defaults. User overrides are read from:

- `$XDG_CONFIG_HOME/quickshell/overview/config.json`
- fallback: `~/.config/quickshell/overview/config.json`

> **Note:** After editing `config.json`, manually restart overview for changes to apply:
> `qs ipc -c overview call overview close && qs -c overview`

### Quick Start

```bash
mkdir -p ~/.config/quickshell/overview
cp /etc/xdg/quickshell/overview/config.example.json ~/.config/quickshell/overview/config.json
```

If you installed from git clone instead of AUR, copy from your repo path:

```bash
cp ~/.config/quickshell/overview/config.example.json ~/.config/quickshell/overview/config.json
```

### Workspace Grid

Edit `~/.config/quickshell/overview/config.json`:

```json
{
  "overview": {
    "rows": 2,
    "columns": 5,
    "scale": 0.16,
    "enable": true,
    "hideEmptyRows": true,
    "useWorkspaceMap": false,
    "workspaceMap": [0, 10],
    "orderRightLeft": false,
    "orderBottomUp": false,
    "previewsEnabled": true,
    "previewMode": "live",
    "includeInactiveMonitorPreviews": true,
    "previewRecaptureDelayMs": 60,
    "workspaceSpacing": 5,
    "backgroundPadding": 10,
    "workspaceNumberBaseSize": 250
  }
}
```

**Common adjustments:**
- **Too small?** Increase `scale` (try 0.20 or 0.25)
- **Too big?** Decrease `scale` (try 0.12 or 0.14)
- **More workspaces?** Change `rows` and `columns` (e.g., 3 rows × 4 columns = 12 workspaces)
- **Reverse order?** Set `orderRightLeft` and/or `orderBottomUp` to `true`
- **Per-monitor workspace groups?** Enable `useWorkspaceMap` and set `workspaceMap` (e.g. `[0,10]`)
- **Lower memory use?** Set `previewMode` to `event` and `includeInactiveMonitorPreviews` to `false`

**Hide empty workspace rows:**
- Set `hideEmptyRows: true` to automatically hide rows that have no windows
- Keeps your overview clean by only showing rows with active workspaces
- The current workspace row is always visible, even if empty
- Arrow key navigation (left/right) stays within the current row when enabled
- Great for 2-row setups where you rarely use workspaces 6-10

### Position

Edit `~/.config/quickshell/overview/config.json`:

```json
{
  "position": {
    "topMargin": 100
  }
}
```

Increase `topMargin` to move the overview down. Decrease it to move up.

### Window Preview

```json
{
  "windowPreview": {
    "iconToWindowRatio": 0.25,
    "iconToWindowRatioCompact": 0.45,
    "xwaylandIndicatorToIconRatio": 0.35,
    "inactiveMonitorOpacity": 0.4
  }
}
```

### Performance Tuning

```json
{
  "overview": {
    "previewsEnabled": true,
    "previewMode": "live",
    "includeInactiveMonitorPreviews": true,
    "previewRecaptureDelayMs": 60
  },
  "hacks": {
    "hyprlandEventDebounceMs": 40
  }
}
```

- `overview.previewsEnabled`: turn all window screencopy previews on/off
- `overview.previewMode`: `live` (best visuals, more RAM) or `event` (lower RAM, refreshes on window events)
- `overview.includeInactiveMonitorPreviews`: when `false`, only current monitor windows get preview capture
- `overview.previewRecaptureDelayMs`: delay used for event-mode snapshot refresh (lower = faster updates)
- `hacks.hyprlandEventDebounceMs`: coalesces Hyprland event refreshes to reduce command churn

Low-memory preset:

```json
{
  "overview": {
    "previewMode": "event",
    "includeInactiveMonitorPreviews": false
  },
  "hacks": {
    "hyprlandEventDebounceMs": 80
  }
}
```

### Full Example

```json
{
  "appearance": {
    "colorSource": "default",
    "caelestia": {
      "autoRefresh": true,
      "refreshInterval": 2000,
      "accentProfile": "vibrant"
    },
    "rounding": {
      "unsharpen": 2,
      "verysmall": 8,
      "small": 12,
      "normal": 17,
      "large": 23,
      "full": 9999,
      "screenRounding": 23,
      "windowRounding": 18
    },
    "font": {
      "family": {
        "main": "sans-serif",
        "title": "sans-serif",
        "expressive": "sans-serif"
      },
      "pixelSize": {
        "smaller": 12,
        "small": 15,
        "normal": 16,
        "larger": 19,
        "huge": 22
      }
    },
    "animation": {
      "duration": {
        "elementMove": 500,
        "elementMoveEnter": 400,
        "elementMoveFast": 200
      }
    },
    "sizes": {
      "elevationMargin": 10
    }
  },
  "overview": {
    "rows": 2,
    "columns": 5,
    "scale": 0.16,
    "enable": true,
    "hideEmptyRows": true,
    "useWorkspaceMap": false,
    "workspaceMap": [0, 10],
    "orderRightLeft": false,
    "orderBottomUp": false,
    "previewsEnabled": true,
    "previewMode": "live",
    "includeInactiveMonitorPreviews": true,
    "previewRecaptureDelayMs": 60,
    "workspaceSpacing": 5,
    "backgroundPadding": 10,
    "workspaceNumberBaseSize": 250
  },
  "position": {
    "topMargin": 100
  },
  "windowPreview": {
    "iconToWindowRatio": 0.25,
    "iconToWindowRatioCompact": 0.45,
    "xwaylandIndicatorToIconRatio": 0.35,
    "inactiveMonitorOpacity": 0.4
  },
  "hacks": {
    "arbitraryRaceConditionDelay": 150,
    "hyprlandEventDebounceMs": 40
  }
}
```

### Theme & Colors

Most theme sizing/timing options are now configurable via `config.json`:
- `appearance.colorSource` (`default`, `matugen`, `caelestia`)
- `appearance.caelestia.*` (`autoRefresh`, `refreshInterval`, `accentProfile`)
- `appearance.rounding.*`
- `appearance.font.*`
- `appearance.animation.duration.*`
- `appearance.sizes.elevationMargin`

For full color palette customization, edit `~/.config/quickshell/overview/common/Appearance.qml`.

### Matugen (Dynamic Colors from Wallpaper)

[Matugen](https://github.com/InioX/matugen) lets you generate Material You colors from your wallpaper and apply them to the overview automatically.

**1. Install matugen** - follow [matugen's install guide](https://github.com/InioX/matugen?tab=readme-ov-file#installation)

**2. Copy the template** from this repo to matugen's templates folder:
```bash
mkdir -p ~/.config/matugen/templates
cp ~/.config/quickshell/overview/quickshell-overview.qml ~/.config/matugen/templates/
```

**3. Add this to `~/.config/matugen/config.toml`** (create the file if it doesn't exist):
```toml
[templates.quickshell_overview]
input_path  = "./templates/quickshell-overview.qml"
output_path = "~/.config/quickshell/overview/common/Appearance.colors.qml"
```

**4. Enable it** in `~/.config/quickshell/overview/config.json`:
```json
{
  "appearance": {
    "colorSource": "matugen"
  }
}
```

**5. Run matugen** with your wallpaper to generate colors:
```bash
matugen image /path/to/your/wallpaper.jpg
```

This generates `Appearance.colors.qml` which the overview loads automatically. Re-run step 5 whenever you change your wallpaper.

### Caelestia

If you use Caelestia, set the source to `caelestia`:

```json
{
  "appearance": {
    "colorSource": "caelestia",
    "caelestia": {
      "autoRefresh": true,
      "refreshInterval": 2000,
      "accentProfile": "vibrant"
    }
  }
}
```

Overview reads the active palette from `caelestia scheme get` and refreshes it live when `autoRefresh` is enabled, so wallpaper/scheme changes can apply without restarting overview.

---

## 📋 Requirements

- **Hyprland** compositor (tested on latest versions)
- **Quickshell** (Qt6-based shell framework)
- **Qt 6** with the following modules:
  - QtQuick
  - QtQuick.Controls
  - QtQuick.Layouts
  - Quickshell.Wayland
  - Quickshell.Hyprland

## 🚫 Removed Features (from original illogical-impulse)

The following features were removed to make it standalone:

- App search functionality
- Emoji picker
- Clipboard history integration
- Search widget
- Integration with the full illogical-impulse shell ecosystem

## 📁 File Structure

```
~/.config/quickshell/overview/
├── shell.qml                      # Main entry point
├── README.md                      # This file
├── config.example.json            # User override template
├── hyprland-config.conf          # Configuration reference
├── common/
│   ├── Appearance.qml            # Theme and styling
│   ├── Config.qml                # Default config + user override loader
│   ├── functions/
│   │   └── ColorUtils.qml        # Color manipulation utilities
│   └── widgets/
│       ├── StyledText.qml        # Styled text component
│       ├── StyledRectangularShadow.qml
│       ├── StyledToolTip.qml
│       └── StyledToolTipContent.qml
├── services/
│   ├── GlobalStates.qml          # Global state management
│   └── HyprlandData.qml          # Hyprland data provider
└── modules/
    └── overview/
        ├── Overview.qml          # Main overview component
        ├── OverviewWidget.qml    # Workspace grid widget
        └── OverviewWindow.qml    # Individual window preview
```

## 🎯 IPC Commands

```bash
# Toggle overview
qs ipc -c overview call overview toggle

# Open overview
qs ipc -c overview call overview open

# Close overview  
qs ipc -c overview call overview close
```

## 🐛 Known Issues

- Window icons may fallback to generic icon if app class name doesn't match icon theme
- Potential crashes during rapid window state changes due to Wayland screencopy buffer management

## 💖 Support

If this project helps your setup and you want to support continued maintenance, you can sponsor here:

https://github.com/sponsors/Shanu-Kumawat

##  Credits

Extracted from the overview feature in [illogical-impulse](https://github.com/end-4/dots-hyprland) by [end-4](https://github.com/end-4).

Adapted as a standalone component for Hyprland + Quickshell users who want just the overview functionality.

---

<div align="center">

**Note:** Maintenance will be limited due to time constraints, but **PRs and code improvements are welcome!** Feel free to contribute or fork for your own needs.

Made with ❤️ for the Hyprland community

</div>
