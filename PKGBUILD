# Maintainer: Vextium <Vextium#0001>
# Contributor: Morgan <morganamilo@archlinux.org>

pkgname=paru-git
_pkgname=paru
pkgver=1.11.1.r64.g4a34ae9
pkgrel=1
pkgdesc='Feature packed AUR helper'
url='https://github.com/morganamilo/paru'
source=("git+https://github.com/morganamilo/paru")
backup=("etc/paru.conf")
arch=('i686' 'pentium4' 'x86_64' 'arm' 'armv7h' 'armv6h' 'aarch64')
license=('GPL3')
makedepends=('cargo-nightly' 'clang')
depends=('glibc' 'git' 'pacman')
optdepends=('asp: downloading repo pkgbuilds' 'bat: colored pkgbuild printing' 'devtools: build in chroot')
conflicts=("${_pkgname}")
provides=("${_pkgname}")
sha256sums=(SKIP)

# Rustup install is the same as rustup update if nightly is installed
prepare() {
    cd "${srcdir}/${_pkgname}"
    rustup install nightly
    cargo +nightly update -Z sparse-registry
    cargo +nightly fetch --locked -Z sparse-registry --target "$CARCH-unknown-linux-gnu"
}

build () {
  cd "${srcdir}/${_pkgname}"

  if pacman -T pacman-git > /dev/null; then
    _features+="git,generate,"
  fi

  if [[ $(rustc -V) == *"nightly"* ]]; then
    _features+="backtrace,"
  fi

  if [[ $CARCH != x86_64 ]]; then
    export CARGO_PROFILE_RELEASE_LTO=off
  fi

  PARU_VERSION=${pkgver} cargo +nightly build --frozen --features "${_features:-}" --release --target-dir target -Z sparse-registry
./scripts/mkmo locale/
}

package() {
  cd "${srcdir}/${_pkgname}"

  install -Dm755 target/release/${_pkgname} "${pkgdir}/usr/bin/${_pkgname}"
  install -Dm644 ${_pkgname}.conf "${pkgdir}/etc/${_pkgname}.conf"

  install -Dm644 man/${_pkgname}.8 "${pkgdir}/usr/share/man/man8/${_pkgname}.8"
  install -Dm644 man/${_pkgname}.conf.5 "${pkgdir}/usr/share/man/man5/${_pkgname}.conf.5"

  install -Dm644 completions/bash "${pkgdir}/usr/share/bash-completion/completions/${_pkgname}.bash"
  install -Dm644 completions/fish "${pkgdir}/usr/share/fish/vendor_completions.d/${_pkgname}.fish"
  install -Dm644 completions/zsh "${pkgdir}/usr/share/zsh/site-functions/_${_pkgname}"

  install -d "${pkgdir}/usr/share/"
  cp -r locale "${pkgdir}/usr/share/"
}

pkgver() {
  cd "${srcdir}/${_pkgname}"
  git describe --long --tags | sed 's/^v//;s/\([^-]*-g\)/r\1/;s/-/./g'
}
