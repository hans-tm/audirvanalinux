# audirvana-studio (Arch Linux / AUR package)

Arch Linux packaging of **[Audirvana Studio](https://audirvana.com/)**, a high-quality audio player.
This package repackages the official `.deb` that Audirvana distributes for Debian/Ubuntu so it can be installed on Arch Linux and derivatives (e.g. AudioLinux, EndeavourOS, Manjaro).

Based on the AUR package: <https://aur.archlinux.org/packages/audirvana-studio>

> **Note:** Audirvana Studio is proprietary software and requires a subscription/license from Audirvana. This repository only contains packaging scripts — no Audirvana binaries are distributed here. The binary is downloaded from Audirvana's official server at build time.

## Contents

| File | Purpose |
|---|---|
| `PKGBUILD` | Arch build script — downloads the official `.deb` and repackages it |
| `.SRCINFO` | Generated package metadata for the AUR |
| `audirvana-studio.install` | Post-install/upgrade messages |
| `audirvanaStudio.service` | systemd user service to run Audirvana Studio headless |
| `qbzd/` | Separate package: headless [Qobuz Connect daemon](https://github.com/vicrodh/qbz) with a system service unit (`cd qbzd && makepkg -si`) |
| `airplay/` | Separate package: AirPlay 2 endpoint — pulls in `shairport-sync` from the Arch repos and adds the Infigo config (DAC output, device name, audio-core pinning) (`cd airplay && makepkg -si`) |

## Building and installing

```sh
git clone <this-repo-url>
cd audirvanalinux
makepkg -si
```

Dependencies: `glibc`, `gcc-libs`, `alsa-lib`, `avahi`, `curl`, `libxml2`.

## First-time setup

Create the configuration directory and accept the EULA:

```sh
mkdir -p ~/.config/audirvana
echo 'EulaAccepted = 1' > ~/.config/audirvana/audirvana-studio.ini
```

Enable Avahi (required for network discovery / remote control):

```sh
sudo systemctl enable --now avahi-daemon
```

## Running

Audirvana Studio runs as a headless service, controlled from the Audirvana Remote app (iOS/Android):

```sh
# Start now
systemctl --user start audirvanaStudio

# Start automatically at login
systemctl --user enable audirvanaStudio
```

## Choosing the Audirvana version

The `PKGBUILD` defaults to the current release, **3.4.0**. It can also build earlier supported versions, which is how a streamer is rolled back:

```sh
makepkg -s                              # 3.4.0-1 (default)
AUDIRVANA_VERSION=3.3.4 makepkg -s      # 3.3.4-2, the previous fleet version
```

Then install the result with `sudo pacman -U audirvana-studio-<version>-x86_64.pkg.tar.zst`. Pacman warns that it is downgrading, which is expected for a rollback. Restart the service afterwards.

The git tag `audirvana-3.3.4-2` also marks the last commit where 3.3.4 was the only version, so `git checkout audirvana-3.3.4-2 && makepkg -s` works as well.

### If Audirvana stops hosting an old `.deb`

The binary is downloaded from Audirvana's server at build time and is not stored in this repository. If an older `.deb` disappears from that server, copy a saved copy of it next to the `PKGBUILD` before building. `makepkg` uses a local file with the same name instead of downloading it, and still verifies its checksum:

```sh
cp /path/to/audirvana-studio_3.3.4_amd64.deb .
AUDIRVANA_VERSION=3.3.4 makepkg -s
```

## Updating to a new Audirvana version

When Audirvana releases a new version:

1. Add the new version to the `_deb_sha256` and `_pkgrel` tables in `PKGBUILD`, with the `.deb`'s SHA-256 and pkgrel `1`. Keep the older entries so rollbacks keep working.
2. Set `_default_ver` to the new version.
3. Regenerate `.SRCINFO` on an Arch machine:
   ```sh
   makepkg --printsrcinfo > .SRCINFO
   ```
4. Rebuild with `makepkg -si`.

## License

The packaging scripts in this repository are provided as-is. Audirvana Studio itself is proprietary software © Audirvana — see the license installed to `/usr/share/licenses/audirvana-studio/` after installation.
