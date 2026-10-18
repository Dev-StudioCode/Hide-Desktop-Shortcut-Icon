# Remove Shortcut Arrow Overlays (Windhawk Mod)

[![Windhawk Mod](https://img.shields.io/badge/Windhawk-Mod-blue.svg)](https://windhawk.net/)
[![Platform](https://img.shields.io/badge/Platform-Windows%2010%20%7C%20Windows%2011-0078D6.svg)](https://microsoft.com/windows)
[![Architecture](https://img.shields.io/badge/Architecture-x86--64-brightgreen.svg)](#requirements)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A clean, modern [Windhawk](https://windhawk.net/) mod that removes the shortcut arrow overlay from desktop and File Explorer icons in Windows. 

Unlike traditional methods that rely on Windows Registry hacks, this mod dynamically operates directly in memory via the Windows Shell Image List APIs, eliminating visual glitches like the infamous black square artifact.

---

## 🌟 Why This Mod?

For years, the standard way to hide shortcut arrows involved creating a registry key under `Shell Icons` pointing to a blank `.ico` file. While popular, this legacy registry method is notorious for:
* Causing **black squares/black boxes** over desktop icons after Windows updates or icon cache rebuilds.
* Requiring elevated permissions to drop foreign `.ico` files into protected directories like `C:\Windows\System32`.
* Requiring explorer restarts or full system reboots to apply or revert.

### Windhawk Mod vs. Registry Tweaks

| Feature | Legacy Registry Hack | This Windhawk Mod |
| :--- | :---: | :---: |
| **Registry Modifications** | ⚠️ Yes (`Shell Icons\29`) | 🟢 **None (0% registry changes)** |
| **External Files Required** | ⚠️ `blank.ico` in System32 | 🟢 **None (synthesized in RAM)** |
| **Black Box / Black Square Bug** | ❌ Frequent after updates/high-DPI | 🟢 **Immune (proper 32-bit ARGB)** |
| **Reversibility** | ⚠️ Re-edit registry & restart Explorer | 🟢 **Instant toggle via Windhawk UI** |
| **Multi-Resolution / High-DPI** | ❌ Often renders improperly scaled | 🟢 **Handles all icon size lists** |
| **Custom Overlays Support** | ❌ Difficult to configure | 🟢 **Supported via Mod Settings** |

---

## ✨ Features

- **Zero Registry Tampering**: Operates purely in-memory within `explorer.exe`. Your registry remains clean and untouched.
- **Dynamic In-Memory Synthesis**: Generates a 100% transparent 32-bit ARGB icon directly in RAM using Win32 GDI. No external `.ico` files are needed.
- **All Shell Icon Sizes Handled**: Targets all system image list sizes (`SHIL_LARGE`, `SHIL_SMALL`, `SHIL_EXTRALARGE`, `SHIL_SYSSMALL`, `SHIL_JUMBO`), ensuring flawless display across all view modes and DPI scales.
- **Instant Activation & Reversibility**: Instantly applies when enabled and cleanly restores original Windows shortcut arrows when disabled or unloaded—no Explorer restarts needed.
- **Custom Overlay Option**: Prefer a custom subtle badge or tiny dot instead of completely invisible arrows? You can easily set a path to your own custom `.ico` file in the mod settings.

---

## 🛠️ How It Works

1. **Shell Image List Hooking**: The mod retrieves the system shell icon overlay index for links (`IDO_SHGIOI_LINK`) via `SHGetIconOverlayIndexW`.
2. **Dynamic Icon Synthesis**: It creates an in-memory 32-bit DIB section with complete alpha transparency (`bi.bV5AlphaMask = 0xFF000000`) and a valid 1-bit monochrome mask.
3. **Overlay Replacement**: Across all shell image lists obtained from `SHGetImageList`, it replaces the link overlay icon using `IImageList::ReplaceIcon` and binds it with `IImageList::SetOverlayImage`.
4. **Non-Destructive Cleanup**: When the mod is disabled, original overlay indices are restored from backup structures in memory.
5. **Seamless Refresh**: Notifies the shell using `SHChangeNotify(SHCNE_ASSOCCHANGED)` and invalidates the desktop list view to refresh existing icons immediately.

---

## 🚀 Installation & Usage

### Method 1: Using Windhawk (Local / Developer Mode)

1. Download and install [Windhawk](https://windhawk.net/) (v1.4 or newer).
2. Open Windhawk and open the **Details** menu (top-right menu `...`) > **Advanced** > enable **Developer mode**.
3. Go to the **Mods** tab and click **Create New Mod**.
4. Set the Mod ID to:
   ```text
   remove-shortcut-arrows
   ```
5. Copy the entire source code from [`remove-shortcut-arrows.wh.cpp`](remove-shortcut-arrows.wh.cpp) and paste it into the Windhawk code editor.
6. Click **Compile & Save**.
7. Windhawk will compile the mod and inject it into `explorer.exe`. All shortcut arrows will immediately disappear!

---

## ⚙️ Configuration

The mod works out of the box with zero configuration. If you wish to use a custom icon instead of an invisible overlay:

1. Open Windhawk and click on **Remove Shortcut Arrow Overlays**.
2. Switch to the **Settings** tab.
3. In the **Custom Overlay Icon Path** field, enter the full path to a `.ico` file:
   ```text
   C:\Icons\custom-arrow.ico
   ```
4. Click **Save Settings**. Leave the field empty at any time to return to invisible overlays.

---

## 📋 Requirements & Compatibility

- **Operating System**: Windows 10 & Windows 11 (x86, x86-64, ARM64)
- **Scope**: Modifies the system image lists of `explorer.exe` (Desktop & File Explorer). Common Open/Save file dialogs hosted inside other third-party application processes maintain separate process image lists and will display stock arrows.
- **Host Software**: [Windhawk](https://windhawk.net/) (v1.4 or newer)
- **Dependencies**: None (Uses standard Windows APIs: `gdi32`, `shell32`, `user32`)

---

## 📁 Repository Structure

```text
├── .gitattributes                # Git line-ending configuration
├── README.md                     # Documentation and usage guide
└── remove-shortcut-arrows.wh.cpp # Windhawk mod source code
```

> **Note on Legacy Files**: Older methods used `.reg` files and standalone `.ico` files. Those are not required by this mod and should not be used, as they can cause black box artifacts on modern versions of Windows.

---

## 👏 Credits & Acknowledgements

* Credit to the **Hide Desktop Icon Text and Shortcut Arrows** mod (`hide-desktop-icon-text`) by **m417z** for the transparent GDI icon synthesis and system image list overlay replacement approach.
* This mod provides a dedicated, focused implementation featuring clean in-place icon replacement (no image slot leaks), instant reversibility on unload without restarting Explorer, and custom overlay icon support.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

