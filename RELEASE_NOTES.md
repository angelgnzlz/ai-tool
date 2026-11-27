## Initial Release

First public release of `ai-tool`, a small Bash utility to install, list and
uninstall AppImages in the user's home directory, with automatic icon
extraction and GNOME menu integration.

## Features

- Install AppImages into `~/.local/bin` without `sudo`.
- Copy the AppImage (the original file is preserved).
- Check runtime dependencies before installing: verifies that the AppImage can
  be extracted (FUSE) and that its main binary has all shared libraries
  available. If something is missing, shows the install hints for your
  distribution and asks whether to continue.
- Detect the application version from `X-AppImage-Version` in the bundled
  `.desktop` file, or by running the AppImage with `--version` under a timeout.
- Extract the original icon from the AppImage via `--appimage-extract`.
- Auto-detect `StartupWMClass` from the `.desktop` file bundled inside the
  AppImage, with an optional third argument to override it.
- Generate the `.desktop` entry in `~/.local/share/applications`.
- List and uninstall AppImages installed with the tool itself.
- Secure self-update via `--update`, with SHA256 checksum verification.
- Works on `x86_64` and `arm64` (the script is architecture-independent).

## Installation

```bash
git clone https://github.com/angelgnzlz/ai-tool.git
cd ai-tool
install -Dm755 ai-tool ~/.local/bin/ai-tool
```

Make sure `~/.local/bin` is in your `$PATH`:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

## Usage

```bash
ai-tool --help
ai-tool -i ~/Downloads/Obsidian-1.14.5-arm64.AppImage "Obsidian"
ai-tool --list
ai-tool -u Obsidian
ai-tool --update
```

If the GNOME dock shows a generic icon or the window's internal name, pass the
`WM_CLASS` explicitly as the third argument:

```bash
ai-tool -i ~/Downloads/Obsidian-1.14.5-arm64.AppImage "Obsidian" "md.obsidian.Obsidian"
```

## Requirements

- `bash` >= 4.0
- `coreutils`, `findutils`, `sed`, `grep`, `awk`
- FUSE (`libfuse2` on Debian/Ubuntu, `fuse`/`fuse-libs` on Fedora,
  `fuse2` on Arch) to run AppImages
- `update-desktop-database` (optional, recommended; package `desktop-file-utils`)
- `curl` and `sha256sum` (only required for `--update`)

## Full Changelog

https://github.com/angelgnzlz/ai-tool/commits/v1.0.0