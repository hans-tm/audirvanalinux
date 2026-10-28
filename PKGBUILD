# Maintainer: Audiolinux  audiolinux@fastmail.fm

# Audirvana version to package. Default is the current release.
# Build an older supported version (e.g. to roll a streamer back) with:
#   AUDIRVANA_VERSION=3.3.4 makepkg -s
_default_ver=3.4.0
declare -A _deb_sha256=(
  [3.4.0]='61f46ea7b9df52195eddd76e0fa19421787c0330cc162f3599eff2ebd1a0a7da'
  [3.3.4]='4c047af90af7c0a55776eed80b3f129b9837d89980c9b9be90e9ecd63e91560a'
)
# pkgrel per version, so a rebuilt rollback package keeps the version it had in the fleet.
declare -A _pkgrel=(
  [3.4.0]=1
  [3.3.4]=2
)
_ver=${AUDIRVANA_VERSION:-$_default_ver}
if [[ -z ${_deb_sha256[$_ver]} ]]; then
  echo "Unsupported AUDIRVANA_VERSION '$_ver'. Supported: ${!_deb_sha256[*]}" >&2
  exit 1
fi

pkgname=audirvana-studio
pkgver=$_ver
pkgrel=${_pkgrel[$_ver]}
pkgdesc="Audirvana Studio audio player"
arch=('x86_64')
url="https://audirvana.com/"
license=('custom')
depends=('glibc' 'gcc-libs' 'alsa-lib' 'avahi' 'curl' 'libxml2')
source=("https://audirvana.com/delivery/linux/apt-repo/pool/non-free/audirvana-studio/audirvana-studio_"$pkgver"_amd64.deb" 'audirvanaStudio.service')
sha256sums=("${_deb_sha256[$_ver]}"
'b8ed5b2c53a9fad79a6793700821002b0467cae4509239d3f86af7e5735bbfa9')
install=${pkgname}.install

package() {
bsdtar xf data.tar.gz -C "$pkgdir"
install -Dm644 "audirvanaStudio.service" "$pkgdir/usr/lib/systemd/user/audirvanaStudio.service"
install -Dm644 "$pkgdir/opt/audirvana/studio/share/CREDITS" "$pkgdir/usr/share/licenses/$pkgname/CREDITS"
install -Dm644 "$pkgdir/opt/audirvana/studio/share/LICENSE" "$pkgdir/usr/share/licenses/$pkgname/COPYING"
#chmod 4755 "$pkgdir/opt/audirvana/studio/smb_mount_helper"
rm -rf "$pkgdir/opt/audirvana/studio/share/etc"
rm -f "$pkgdir/opt/audirvana/studio/share/CREDITS"
rm -f "$pkgdir/opt/audirvana/studio/share/LICENSE"
}
