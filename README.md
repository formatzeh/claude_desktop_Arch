***THIS IS AN UNOFFICIAL BUILD SCRIPT!***

If you run into an issue with this build script, make an issue here. Don't bug Anthropic about it - they already have enough on their plates.

# Claude Desktop for Archy Linux

This project was inspired by [k3d3's claude-desktop-linux-flake](https://github.com/k3d3/claude-desktop-linux-flake). Their work provided valuable insights into the application's structure and the native bindings implementation.

Supports MCP!

Location of the MCP-configuration file is: `~/.config/Claude/claude_desktop_config.json`

![image](https://github.com/user-attachments/assets/93080028-6f71-48bd-8e59-5149d148cd45)

Supports the Ctrl+Alt+Space popup!
![image](https://github.com/user-attachments/assets/1deb4604-4c06-4e4b-b63f-7f6ef9ef28c1)

Supports the Tray menu! (Screenshot of running on KDE)
![image](https://github.com/user-attachments/assets/ba209824-8afb-437c-a944-b53fd9ecd559)

# Installation Options

## 1. Arch Package

For Arch-based distributions, you can build and install Claude Desktop using the provided build script:

```bash
# Clone this repository
git https://github.com/formatzeh/claude_desktop_Arch.git
cd claude_desktop_Arch

## Build and install the package
makepkg -si
```

## This will:
   - Download Claude-Setup-x64.exe.
   - Extract the resources.
   - Stub out native calls.
   - Package them into claude-desktop-0.8.0-1-x86_64.pkg.tar.zst.
   - Prompt you to install with pacman.

## After install, you can run:
```bash
claude-desktop
```

## To uninstall, just run:
```bash
sudo pacman -R claude-desktop
```
