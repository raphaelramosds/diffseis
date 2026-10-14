# viewsu deployment

1. Build the PyInstaller executable

Install the build tools:

```bash
sudo apt update
sudo apt install python3-tk python3-venv wget
```

Create the environment:

```bash
python3 -m venv .venv-build
source .venv-build/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
pip install pyinstaller
```

Build the viewer:

```bash
pyinstaller \
  --clean \
  --onefile \
  --windowed \
  --name diffseis-viewer \
  app.py
```

2. Create the AppDir layout

```bash
rm -rf AppDir
mkdir -p AppDir/usr/bin
cp dist/diffseis-viewer AppDir/usr/bin/
```

Create the desktop entry:

```bash
cat > AppDir/diffseis-viewer.desktop <<'EOF'
[Desktop Entry]
Name=DiffSeis SU Viewer
Comment=View Seismic Unix files
Exec=diffseis-viewer
Terminal=false
Type=Application
Categories=Science;
Icon=diffseis-viewer
EOF
```

Download a generic PNG icon using the same basename as the desktop entry:

```bash
wget -O AppDir/diffseis-viewer.png \
  https://raw.githubusercontent.com/twitter/twemoji/master/assets/72x72/1f30a.png
```

Create an AppRun launcher:

```bash
cat > AppDir/AppRun <<'EOF'
#!/bin/sh
HERE="$(CDPATH= cd -- "$(dirname -- "$0")" && pwd)"
exec "$HERE/usr/bin/diffseis-viewer" "$@"
EOF

chmod +x AppDir/AppRun
chmod +x AppDir/usr/bin/diffseis-viewer
```

3. Install  appimagetool 

Download the appropriate release for your architecture. For 64-bit Intel/AMD:

```bash
wget -O appimagetool \
  https://github.com/AppImage/appimagetool/releases/latest/download/appimagetool-x86_64.AppImage

chmod +x appimagetool
```

If execution is blocked by FUSE, use:

```bash
./appimagetool --appimage-extract-and-run AppDir DiffSeisViewer-x86_64.AppImage
```

Otherwise:

```bash
./appimagetool AppDir DiffSeisViewer-x86_64.AppImage
```

The resulting file will be: DiffSeisViewer-x86_64.AppImage

Run it with:

```bash
chmod +x DiffSeisViewer-x86_64.AppImage
./DiffSeisViewer-x86_64.AppImage
```