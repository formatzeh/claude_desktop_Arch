# Maintainer: YourName <your.email@domain>
pkgname=claude-desktop-arch
instname=claude-desktop
pkgver=0.8.0
pkgrel=1
pkgdesc="Claude Desktop for Linux (unofficial Arch package)"
arch=('x86_64')
url="https://www.anthropic.com/index/claude"
license=('custom')
depends=('p7zip' 'icoutils' 'imagemagick' 'nodejs' 'npm')
makedepends=('asar')  # We need the asar tool at build time
source=("Claude-Setup-x64.exe::https://storage.googleapis.com/osprey-downloads-c02f6a0d-347c-492b-a752-3e0651722e97/nest-win-x64/Claude-Setup-x64.exe")

# The SHA256 is optional but recommended. The example here is a placeholder
sha256sums=('SKIP')

# Optional: if the AnthropicClaude-$pkgver-full.nupkg name changes, adjust in build()
# Also note the portion extracting icons or i18n stubs might vary if their internal structure changes in future versions.

build() {

  # Function to check if a command exists
  check_command() {
      if ! command -v "$1" &> /dev/null; then
          echo "❌ $1 not found"
          return 1
      else
          echo "✓ $1 found"
          return 0
      fi
  }

  # Install electron globally via npm if not present
  if ! check_command "electron"; then
      echo "Installing electron via npm..."
      sudo npm install -g electron
      if ! check_command "electron"; then
          echo "Failed to install electron. Please install it manually:"
          echo "sudo npm install -g electron"
          exit 1
      fi
      echo "Electron installed successfully"
  fi
  
  cd "$srcdir"

  # 1. Extract Claude-Setup-x64.exe
  7z x -y "Claude-Setup-x64.exe" || return 1

  # 2. Extract the nupkg
  7z x -y "AnthropicClaude-$pkgver-full.nupkg" || return 1

  # 3. Extract icons from Windows EXE
  wrestool -x -t 14 "lib/net45/claude.exe" -o claude.ico
  icotool -x claude.ico

  # 4. Prepare electron-app
  mkdir -p electron-app
  cp "lib/net45/resources/app.asar" "electron-app/"
  cp -r "lib/net45/resources/app.asar.unpacked" "electron-app/"

  # 5. Stub native module
  cd electron-app
  npx asar extract app.asar app.asar.contents

  # Insert the stub index.js for claude-native
  mkdir -p app.asar.contents/node_modules/claude-native
  cat > app.asar.contents/node_modules/claude-native/index.js << EOF
// Stub for claude-native
const KeyboardKey = {
  Backspace: 43,
  Tab: 280,
  Enter: 261,
  Shift: 272,
  Control: 61,
  Alt: 40,
  CapsLock: 56,
  Escape: 85,
  Space: 276,
  PageUp: 251,
  PageDown: 250,
  End: 83,
  Home: 154,
  LeftArrow: 175,
  UpArrow: 282,
  RightArrow: 262,
  DownArrow: 81,
  Delete: 79,
  Meta: 187
};
Object.freeze(KeyboardKey);

module.exports = {
  getWindowsVersion: () => "10.0.0",
  setWindowEffect: () => {},
  removeWindowEffect: () => {},
  getIsMaximized: () => false,
  flashFrame: () => {},
  clearFlashFrame: () => {},
  showNotification: () => {},
  setProgressBar: () => {},
  clearProgressBar: () => {},
  setOverlayIcon: () => {},
  clearOverlayIcon: () => {},
  KeyboardKey
};
EOF

  # If needed, create a stub en-US.json for i18n
  mkdir -p app.asar.contents/resources/i18n
  cat > app.asar.contents/resources/i18n/en-US.json << EOF
{}
EOF

  # Repack app.asar
  npx asar pack app.asar.contents app.asar
}

package() {
  # $pkgdir is the root of our final install
  install -d "$pkgdir/usr/lib/$instname"
  install -d "$pkgdir/usr/bin"
  install -d "$pkgdir/usr/share/applications"
  install -d "$pkgdir/usr/share/icons/hicolor"

  cd "$srcdir"

  # 1. Copy the final app.asar + app.asar.unpacked
  cp electron-app/app.asar "$pkgdir/usr/lib/$instname"
  cp -r electron-app/app.asar.unpacked "$pkgdir/usr/lib/$instname"

  # 2. Create a launcher script
  cat > "$pkgdir/usr/bin/claude-desktop" << EOF
#!/bin/bash
electron /usr/lib/claude-desktop/app.asar "\$@"
EOF
  chmod +x "$pkgdir/usr/bin/claude-desktop"

  # 3. Create .desktop file
  cat > "$pkgdir/usr/share/applications/claude-desktop.desktop" <<EOF
[Desktop Entry]
Name=Claude
Exec=claude-desktop %u
Icon=claude-desktop
Type=Application
Terminal=false
Categories=Office;Utility;
MimeType=x-scheme-handler/claude;
StartupWMClass=Claude
EOF

  # 4. Install icons
  # If you extracted them in build() you have claude_X_Y.png in $srcdir
  # For example:
  install -Dm644 claude_13_16x16x32.png "$pkgdir/usr/share/icons/hicolor/16x16/apps/claude-desktop.png" || true
  install -Dm644 claude_11_24x24x32.png "$pkgdir/usr/share/icons/hicolor/24x24/apps/claude-desktop.png" || true
  install -Dm644 claude_10_32x32x32.png "$pkgdir/usr/share/icons/hicolor/32x32/apps/claude-desktop.png" || true
  install -Dm644 claude_8_48x48x32.png  "$pkgdir/usr/share/icons/hicolor/48x48/apps/claude-desktop.png" || true
  install -Dm644 claude_7_64x64x32.png  "$pkgdir/usr/share/icons/hicolor/64x64/apps/claude-desktop.png" || true
  install -Dm644 claude_6_256x256x32.png "$pkgdir/usr/share/icons/hicolor/256x256/apps/claude-desktop.png" || true
}
