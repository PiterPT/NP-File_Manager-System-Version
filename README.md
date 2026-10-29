# NP File Manager System

A custom Python graphical application designed to automate the sequential, temporary replacement of game/configuration files for the **Ninja Project** launcher, featuring advanced visual button detection and seamless looping multimedia playback.

## 📌 Project Overview

**NP File Manager System** operates during two critical phases throughout the game client startup:

1. **Step 1 (Pre-Launch):** Replaces targeted game configuration and asset files with the original cycle files.
2. **Launcher Monitoring:** Spawns `NinjaProject.exe` and monitors the display until the "Play" / "Jogar" button becomes ready (supporting multi-resolution image templates to handle different Windows DPI scaling factors such as 100%, 125%, 150%, and 175%).
3. **Step 2 (Post-Detection):** Swaps the target files with the modified assets and automates the double-click action to enter the game.

### Core Stack & Libraries

* **GUI & Styling:** `tkinter`, `ttk` (custom borderless UI powered by `overrideredirect`).
* **Video:** `OpenCV` (`cv2`) and `Pillow` (`PIL`) for seamless looping playback and real-time aspect-fit scaling.
* **Audio:** `pygame.mixer` for looping background music with volume control and muting persistence.
* **Automation & Vision:** `pyautogui`, `pygetwindow`, `ctypes` (native Windows Per-Monitor DPI Awareness enabled).
* **Packaging:** `PyInstaller` (standalone single-file executable bundling all local assets).

---

## 🛠️ Build & Compilation Instructions

Before compiling, ensure any active instances of the application are terminated to prevent file-lock errors:

```cmd
taskkill /f /im FileManagerSystem-v1.1.6.exe 2>nul
```

### Option 1: Build via `.spec` File (Recommended)

This is the preferred and cleanest method, ensuring all hidden imports and embedded media files remain consistently structured.

```bash
py -m pyinstaller FileManagerSystem-v1.1.6.spec --clean
```

### Option 2: Full CLI Command (Single Line)

To compile directly without maintaining a separate `.spec` file on disk:

```cmd
py -m pyinstaller --noconfirm --clean --onefile --windowed --name "FileManagerSystem-v1.1.6" ^
  --icon "resources/LaucherICON_v1.ico" ^
  --add-data "resources/intro.mp4;resources" ^
  --add-data "resources/button_100.png;resources" ^
  --add-data "resources/button_125.png;resources" ^
  --add-data "resources/button_150.png;resources" ^
  --add-data "resources/button_175.png;resources" ^
  --add-data "resources/LaucherICON_v1.ico;resources" ^
  --hidden-import "cv2" ^
  --hidden-import "PIL" ^
  --hidden-import "PIL.Image" ^
  --hidden-import "PIL.ImageTk" ^
  --hidden-import "PIL.ImageDraw" ^
  --hidden-import "PIL._tkinter_finder" ^
  --hidden-import "pygame" ^
  --hidden-import "pygame.mixer" ^
  --collect-submodules "pygame" ^
  --exclude-module "imageio" ^
  --exclude-module "imageio_ffmpeg" ^
  --exclude-module "tkvideo" ^
  main.py
```

> **Note:** For PowerShell, replace the caret line-break character (`^`) with a backtick (`` ` ``).

---

## 📄 Expected `.spec` File Content

Save the following configuration as `FileManagerSystem-v1.1.6.spec` in your project's root directory:

```python
# -*- mode: python ; coding: utf-8 -*-
import os
from PyInstaller.utils.hooks import collect_submodules

# Collect all pygame submodules automatically
submodulos_pygame = collect_submodules('pygame')

# Embedded local resources and media assets
recursos_locais = [
    ('resources/intro.mp4', 'resources'),
    ('resources/button_100.png', 'resources'),
    ('resources/button_125.png', 'resources'),
    ('resources/button_150.png', 'resources'),
    ('resources/button_175.png', 'resources'),
    ('resources/LaucherICON_v1.ico', 'resources'),
]

# Conditionally embed intro.mp3 if present
if os.path.isfile('resources/intro.mp3'):
    recursos_locais.append(('resources/intro.mp3', 'resources'))

# Conditionally embed overlay graphic if present
if os.path.isfile('resources/overlay.png'):
    recursos_locais.append(('resources/overlay.png', 'resources'))

a = Analysis(
    ['main.py'],
    pathex=[],
    binaries=[],
    datas=recursos_locais,
    hiddenimports=[
        'cv2',
        'PIL',
        'PIL.Image',
        'PIL.ImageTk',
        'PIL.ImageDraw',
        'PIL._tkinter_finder',
        'pygame',
        'pygame.mixer',
    ] + submodulos_pygame,
    hookspath=[],
    hooksconfig={},
    runtime_hooks=[],
    excludes=['imageio', 'imageio_ffmpeg', 'tkvideo'],
    noarchive=False,
    optimize=0,
)

pyz = PYZ(a.pure)

exe = EXE(
    pyz,
    a.scripts,
    a.binaries,
    a.datas,
    [],
    name='FileManagerSystem-v1.1.6',
    debug=False,
    bootloader_ignore_signals=False,
    strip=False,
    upx=False,
    upx_exclude=[],
    runtime_tmpdir=None,
    console=False,
    disable_windowed_traceback=False,
    argv_emulation=False,
    target_arch=None,
    codesign_identity=None,
    entitlements_file=None,
    icon=['resources/LaucherICON_v1.ico'],
)
```
