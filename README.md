# AppImage Tool

A small Bash utility to **install, list and uninstall AppImages** in the user's
home directory, extracting their original icon and registering them in the
desktop application menu.

## Features

- Installs AppImages into `~/.local/bin` without `sudo`.
- Copies the AppImage (the original file is preserved).
- Checks runtime dependencies (FUSE and shared libraries) before installing.
- Detects the application version from the AppImage metadata.
- Extracts the original icon from the AppImage via `--appimage-extract`.
- Auto-detects `StartupWMClass` from the `.desktop` file bundled inside the
  AppImage, with an optional third argument to override it.
- Generates the `.desktop` entry in `~/.local/share/applications`.
- Supports `StartupWMClass` so the GNOME dock correctly associates the window
  with its launcher.
- Lists and uninstalls AppImages installed with the tool itself.
- Secure self-update via `--update`, with SHA256 checksum verification.
- Works on `x86_64` and `arm64` (the script is architecture-independent).

## Requirements

- `bash` >= 4.0
- `coreutils`, `findutils`, `sed`, `grep`, `awk`
- FUSE (`libfuse2` on Debian/Ubuntu, `fuse`/`fuse-libs` on Fedora,
  `fuse2` on Arch) to run AppImages
- `update-desktop-database` (optional, recommended; package `desktop-file-utils`)
- `curl` and `sha256sum` (only required for `--update`)

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

### Install

```bash
ai-tool -i <path.AppImage> [name] [wm_class]
```

- **`<path.AppImage>`** (required): path to the `.AppImage` file.
- **`[name]`** (optional): name displayed in the menu. If omitted, the file
  name is cleaned up (version numbers and architecture suffixes are removed).
- **`[wm_class]`** (optional): value for `StartupWMClass` in the `.desktop`
  file. If omitted, the script tries to auto-detect it from the `.desktop`
  file bundled inside the AppImage. If no `.desktop` file is found, `[name]`
  is used as the value.

The AppImage is copied to `~/.local/bin`, its icon is extracted to
`~/.local/share/icons`, and the `.desktop` entry is created in
`~/.local/share/applications/<id>.desktop`.

Before copying, the script checks that the AppImage can be extracted (FUSE)
and that its main binary has all shared libraries available. If something is
missing, it shows the install hints for your distribution and asks whether to
continue. It also detects and reports the application version when possible.

### List

```bash
ai-tool --list
```

### Uninstall

```bash
ai-tool -u <id_or_name>
```

Removes the AppImage, its icon and its `.desktop` entry.

### Update

```bash
ai-tool --update
```

Checks the latest release on GitHub, compares it with the installed version,
downloads the new script and its `SHA256SUMS` file, verifies the checksum and
replaces the current script atomically. It requires `curl` and `sha256sum`.

The update is never silent: it asks for confirmation before replacing the
script. If the checksum does not match, the update is aborted and the current
version is left untouched.

## Note about GNOME and `StartupWMClass`

On GNOME, if the dock shows a **generic gear icon** and the **window's internal
name** instead of the defined name, GNOME cannot associate the window with the
`.desktop` file. This is fixed with the `StartupWMClass` key.

- **Default behavior**: the script reads `StartupWMClass` from the `.desktop`
  file bundled inside the AppImage. If that file is missing or does not define
  the key, it falls back to `[name]`.
- **If the icon still doesn't appear**: pass as the third argument the exact
  value GNOME sees for the window. To get it, open the application and hover
  over its window in the dock; the name shown is the `WM_CLASS`.

Example with Obsidian:

```bash
ai-tool -i ~/Downloads/Obsidian-1.14.5-arm64.AppImage "Obsidian" "md.obsidian.Obsidian"
```

This generates a `.desktop` file with `StartupWMClass=md.obsidian.Obsidian`,
which is the value Obsidian declares to GNOME.

If you later want to fix the `WM_CLASS` of an existing installation, edit
`~/.local/share/applications/<id>.desktop` and adjust the `StartupWMClass=`
line, then run `update-desktop-database ~/.local/share/applications`.

## Directory layout

| Path                              | Contents              |
|-----------------------------------|-----------------------|
| `~/.local/bin`                    | Installed AppImages   |
| `~/.local/share/icons`            | Extracted icons       |
| `~/.local/share/applications`     | `.desktop` entries    |

## Releasing

See [RELEASING.md](RELEASING.md) for the release workflow.

## License

MIT. See [LICENSE](LICENSE).