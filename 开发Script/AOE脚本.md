```bash
# -*mode: python ; coding: utf-8 -*from PyInstaller.utils.hooks import collect_data_files

# Collect additional data files
datas = collect_data_files("PIL")

a = Analysis(
    ['AOE4.py'],
    pathex=[],
    binaries=[],
    datas=datas + [('bk.jpg', '.')],  # Add ('source_file', 'target_directory') for your image file
    hiddenimports=[],
    hookspath=[],
    hooksconfig={},
    runtime_hooks=[],
    excludes=[],
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
    name='AOE4',
    debug=False,
    bootloader_ignore_signals=False,
    strip=False,
    upx=True,
    upx_exclude=[],
    runtime_tmpdir=None,
    console=False,
    disable_windowed_traceback=False,
    argv_emulation=False,
    target_arch=None,
    codesign_identity=None,
    entitlements_file=None,
)
  

# pyinstaller --onefile --windowed AOE4v3.py

```