# 🚨🚨🚨🚨🚨🚨🚨VIBECODED!!!!🚨🚨🚨🚨🚨🚨🚨🚨
no i'm not gonna drain your money out of this i'm just lazy and needed a quick program sorry

readme made by clot 
⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️
# mkdesktry

small bash script for making and editing `.desktop` files, mostly so appimages show up in the app menu. one file, no dependencies beyond the usual gnu tools.

made on fedora kde but nothing in it is distro specific.

## install

    sudo install -m755 mkdesktry /usr/local/bin/

or put it in `~/.local/bin` if you don't want sudo. plain `mv` loses the exec bit if you downloaded it, hence `install`.

## using it

    mkdesktry

opens the menu. it lists every `.desktop` it can find (yours, system, flatpak). appimages that don't have an entry yet show up at the top marked with a `✦`.

- number: open an entry, or start a new one if it's a `✦` row
- any text: filter the list. `/` on its own clears it
- `n` / `p` or just enter: next / previous page
- `t`: type a path to an appimage
- `s`: pick a folder and choose from what's in it
- `a`: add every new appimage at once with default settings
- `d`: stop suggesting the current new ones
- `f`: repair broken entries (see below)
- `q`: quit

inside an entry you can edit the raw file in `$EDITOR` (nano if unset), edit the common fields with a small form, relink the appimage, or delete it. system entries can't be changed in place, so editing one copies it to `~/.local/share/applications` first and the copy overrides the original. deleting that copy brings the original back.

### instant mode

    mkdesktry ./Some_App-1.2.3-x86_64.AppImage
    mkdesktry ./Some_App.AppImage -n "Some App" -c "does things" -g "Network;Chat;" -t

| flag | what |
|------|------|
| `-n` | name, default is guessed from the filename |
| `-c` | comment |
| `-g` | categories, default `Utility;` |
| `-i` | icon path or theme name, default is pulled out of the appimage |
| `-t` | run in a terminal |

### broken paths

if you move or rename an appimage the entry stops working. the menu marks those with `✗`.

    mkdesktry fix

goes through them one by one. it looks for the same app under another filename or version (so `Foo-1.2` becomes `Foo-1.3`), and you can also type the new path, scan a folder, or remove the entry. `mkdesktry fix -a` does it without asking and takes the newest match, skipping anything it can't find.

### auto mode

    mkdesktry auto             # one pass over ~/Applications
    mkdesktry auto install     # watch it with a systemd user path unit
    mkdesktry auto uninstall
    mkdesktry auto status

when installed, anything new that lands in `~/Applications` gets an entry with default settings and a notification (if `notify-send` exists). it ignores files that don't look like a finished download, and it never overwrites an existing entry with the same name. i only checked the generated unit files by hand, not against a live systemd, so if the watcher misbehaves look at `systemctl --user status mkdesktry-auto.path`.

## where things go

- entries: `~/.local/share/applications/`
- extracted icons: `~/.local/share/icons/`
- dismissed appimages: `~/.local/share/mkdesktry/ignored`
- systemd units: `~/.config/systemd/user/`

appimages are looked for in `~/Applications`, `~/AppImages`, `~/Downloads`, `~/Desktop`, `~/.local/bin`, `~/bin`, `/opt` and the top level of `~`. override with `MKDESKTRY_DIRS="/a:/b"`. auto mode watches `~/Applications` unless `MKDESKTRY_AUTO_DIR` says otherwise.

## icons

appimages hide their icon behind a chain of symlinks, so the script follows `.DirIcon` all the way down. if that gives nothing it reads the `Icon=` name from the .desktop inside the appimage and digs through `usr/share/icons` and `pixmaps`. extraction uses the appimage's own `--appimage-extract`, so no fuse needed. very old (type 1) appimages probably won't give up their icon, pass `-i` for those. in the menu you can also set an entry's icon to `auto` to try again.

## optional bits

`notify-send`, `update-desktop-database`, `desktop-file-validate`, systemd (only for auto mode). all skipped if they aren't there.🚨
