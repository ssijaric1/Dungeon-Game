# Installers

Cross-platform installers are built by the natID SDK's own `SetupCollector`
(not Inno Setup / NSIS / CPack) from a single config: `installer/dungeonGame.xml`.

| Platform | Output |
|---|---|
| Windows | `Install_dungeonGame.exe` + `dungeonGame.msi` (ship both) |
| macOS | `dungeonGame.app` (libraries in `Contents/Frameworks/`) |
| Linux | `dungeonGame.deb` |

## Getting a release from GitHub

The workflow `.github/workflows/release-all.yml` builds all four (Windows,
macOS arm64, macOS Intel, Linux) and attaches them to a GitHub Release.

Push a tag:

    git tag v1.0.0 && git push origin v1.0.0

or run it from the Actions tab: *Build All Installers* → Run workflow, set
`publish_release` to `yes` and `release_tag` to e.g. `v1.0.0`. Without those it
still runs and leaves the four zips as run artifacts.

## Building the .deb locally

    chmod +x installer/make-linux-installer.sh
    ./installer/make-linux-installer.sh
    sudo apt install ~/natID.RAMDisk/Setup/dungeonGame.deb

Use `apt install`, not `dpkg -i` — dpkg skips the GTK dependencies.

Verify the installed app is really standalone; these must resolve inside
`/usr/lib`, not inside the SDK:

    ldd /usr/bin/dungeonGame | grep -i "natGUI\|mainUtils"

## Things that bite

- `patchelf` must be installed on Linux or the collector fails at the rpath step.
- `$RAMDisk` maps to `/media/RAMDisk` (Linux), `/Volumes/RAMDisk` (macOS), `R:`
  (Windows). Bridge it with a symlink; `/media/RAMDisk` must *be* the symlink.
- The `out=` folder name must match `executableName` exactly — `dpkg-deb --build`
  uses the executable name and fails otherwise.
- Product metadata lives in the root element of `res/DevRes.xml`.
  Without it the collector aborts with `Product displayName cannot be empty!`.
- Only `mainUtils` and `natGUI` are linked, both covered by `natGUIALL`. If that
  ever changes, add a `<Package>` line per extra SDK library or the app dies on a
  machine without the SDK.
- `cat` aliased to a missing `bat` silently truncates heredocs. Use `command cat`.
