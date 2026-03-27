# ClipVault — Windows-style Clipboard Manager for Ubuntu

A pixel-perfect recreation of Windows **Win+V clipboard history** for Ubuntu Linux, built with PyQt5.

---

## Features

| Feature | Windows Win+V | ClipVault |
|---------|--------------|-----------|
| Clipboard History | Yes | Yes |
| Text support | Yes | Yes |
| Image support | Yes | Yes |
| Pin items | Yes | Yes |
| Search history | Yes | Yes |
| Global hotkey | Win+V | Super+V |
| One-click paste | Yes | Yes |
| Dark UI | Yes | Yes |
| System tray | Yes | Yes |
| Auto-start on login | Yes | Yes |
| Tabs (All/Pinned/Text/Images) | Yes | Yes |
| Cross-device sync | Yes (MS account) | No |

---

## Project structure

```
clipboard_manager/
├── app/                        # Application package
│   ├── main.py                 # Entry point
│   ├── shared/
│   │   └── constants.py        # Colors, file paths, limits
│   ├── models/
│   │   └── clip_item.py        # ClipItem data model
│   ├── core/
│   │   ├── history_manager.py  # Load/save/search clipboard history
│   │   └── clipboard_watcher.py# QThread — polls system clipboard
│   ├── workers/
│   │   └── hotkey_listener.py  # Daemon thread — global Super+V hotkey
│   └── ui/
│       ├── main_window.py      # ClipVaultWindow (main UI)
│       ├── tray_icon.py        # System tray icon + context menu
│       └── widgets/
│           └── clip_item_widget.py  # Per-item card widget
├── installation/
│   ├── install.sh              # One-shot installer for Ubuntu/Debian
│   ├── uninstall.sh            # Removes all installed files
│   └── README.md               # Installation guide
├── pyproject.toml              # Project metadata, deps, pytest config
└── README.md
```

---

## Quick install

```bash
chmod +x installation/install.sh
./installation/install.sh
```

Then launch:

```bash
clipvault
```

See [installation/README.md](installation/README.md) for the full installation guide and manual install instructions.

To uninstall:

```bash
chmod +x installation/uninstall.sh
./installation/uninstall.sh
```

---

## Running without installing

```bash
# From the project root
python3 app/main.py
```

---

## Keyboard shortcuts

| Shortcut | Action |
|----------|--------|
| `Super+V` | Toggle clipboard window |
| `Escape` | Close window |
| Click item | Paste immediately |
| Pin button | Pin / Unpin item |
| X button | Delete item |

---

## Configuration

History is stored at `~/.clipvault_history.json`.

To change the max history size, edit `app/shared/constants.py`:

```python
MAX_HISTORY = 50  # change this number
```

---

## Compatibility

- Ubuntu 20.04+
- Debian 11+
- Any GNOME / KDE / XFCE desktop on X11

---

## Wayland limitations

ClipVault is built on PyQt5 + X11. On Wayland sessions some features are restricted:

| Feature | X11 | Wayland |
|---------|-----|---------|
| Clipboard history | Yes | Yes |
| `Super+V` global hotkey | Yes | No |
| Auto-paste on item click | Yes | Partial |
| System tray icon | Yes | Yes (via XWayland) |

**Why the hotkey does not work on Wayland:**
`pynput` uses X11 APIs (`python-xlib`) to intercept global key events. Wayland's security model blocks applications from listening to input outside their own window, so the `Super+V` hotkey cannot be registered.

**Workarounds on Wayland:**
- Click the system tray icon to toggle the window
- Run the session in **X11 mode**: log out → on the login screen click the gear icon → select **Ubuntu on Xorg** → log back in
- Set the environment variable before launching:
  ```bash
  QT_QPA_PLATFORM=xcb clipvault
  ```

**Check which session you are on:**
```bash
echo $XDG_SESSION_TYPE   # prints "x11" or "wayland"
```
