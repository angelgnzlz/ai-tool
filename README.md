# ai-tool

A small Bash utility to **install, list and uninstall AppImages** in the user's
home directory, extracting their original icon and registering them in the
desktop application menu.

## Features

- Installs AppImages into `~/.local/bin` without `sudo`.
- Extracts the original icon from the AppImage via `--appimage-extract`.
- Generates the `.desktop` entry in `~/.local/share/applications`.
- Supports `StartupWMClass` so the GNOME dock correctly associates the window
  with its launcher.
- Lists and uninstalls AppImages installed with the tool itself.
- Works on `x86_64` and `arm64` (the script is architecture-independent).

## Requirements

- `bash` >= 4.0
- `coreutils`, `findutils`, `sed`, `grep`, `awk` (present on any distro)
- `update-desktop-database` (optional, recommended; package `desktop-file-utils`)

## Installation

```bash
git clone https://github.com/YOUR_USERNAME/ai-tool.git
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
ai-tool -i ~/Downloads/Obsidian-1.14.5-arm64.AppImage "Obsidian" "md.obsidian.Obsidian"
ai-tool --list
ai-tool -u Obsidian
```

### Install

```bash
ai-tool -i <path.AppImage> [name] [wm_class]
```

- **`<path.AppImage>`** (required): path to the `.AppImage` file.
- **`[name]`** (optional): name displayed in the menu. If omitted, the file
  name is cleaned up (version numbers and architecture suffixes are removed).
- **`[wm_class]`** (optional): value for `StartupWMClass` in the `.desktop`
  file. **If omitted, `[name]` is used as the value.**

The AppImage is moved to `~/.local/bin`, its icon is extracted to
`~/.local/share/icons`, and the `.desktop` entry is created in
`~/.local/share/applications/<id>.desktop`.

### List

```bash
ai-tool --list
```

### Uninstall

```bash
ai-tool -u <id_or_name>
```

Removes the AppImage, its icon and its `.desktop` entry.

## Note about GNOME and `StartupWMClass`

On GNOME, if the dock shows a **generic gear icon** and the **window's internal
name** instead of the defined name, GNOME cannot associate the window with the
`.desktop` file. This is fixed with the `StartupWMClass` key.

- **Default behavior**: if you don't pass the third argument, the script uses
  `[name]` as `StartupWMClass`. This works for many applications.
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

## License

MIT. See [LICENSE](LICENSE).