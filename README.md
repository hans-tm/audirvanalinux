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

## Updating to a new Audirvana version

When Audirvana releases a new version:

1. Update `pkgver` in `PKGBUILD`.
2. Update the `sha256sums` for the new `.deb` (`updpkgsums` from `pacman-contrib` does this automatically).
3. Regenerate `.SRCINFO`:
   ```sh
   makepkg --printsrcinfo > .SRCINFO
   ```
4. Rebuild with `makepkg -si`.

## License

The packaging scripts in this repository are provided as-is. Audirvana Studio itself is proprietary software © Audirvana — see the license installed to `/usr/share/licenses/audirvana-studio/` after installation.
