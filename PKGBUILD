pkgname=blastem-anthonydotmoe-git
_pkgname=blastem
pkgver=0.0.0.r2055.g184746a
pkgrel=1
pkgdesc="BlastEm (Sega Genesis/Mega Drive emulator) - anthonydotmoe fork"
arch=('x86_64')
url="https://github.com/anthonydotmoe/blastem"
license=('GPL3')

depends=('sdl2' 'glew')
makedepends=('git' 'python' 'python-pillow')

# Optional: only needed if you want to build the ROM-based menu (menu.bin)
optdepends=(
  'vasm: build ROM-based menu UI (menu.bin) (rebuild)'
  'xcftools: build ROM-based menu UI (menu.bin) (rebuild)'
)
provides=("$_pkgname")
conflicts=("$_pkgname")
source=("git+$url.git#branch=libretro")
sha256sums=('SKIP')

pkgver() {
  cd "$_pkgname"
  printf "0.0.0.r%s.g%s" \
    "$(git rev-list --count HEAD)" \
    "$(git rev-parse --short=7 HEAD)"
}

build() {
  cd "$_pkgname"

  make blastem

  # Build menu.bin only if the deps are installed in the build environment.
  # This keeps the package build from hard-failing when vasm/xcftools aren't present.
  if pacman -Qq vasm xcftools >/dev/null 2>&1; then
    make menu.bin
  fi
}

package() {
  cd "$_pkgname"

  local installdir="$pkgdir/opt/blastem-anthonydotmoe"
  install -d "$installdir"

  # Main binary
  install -m 755 blastem "$installdir/blastem"

  # Runtime assets commonly expected next to the executable
  install -m 644 default.cfg "$installdir/default.cfg"
  install -m 644 rom.db gamecontrollerdb.txt "$installdir/"

  # Shaders
  if [[ -d shaders ]]; then
    install -d "$installdir/shaders"
    install -m 644 shaders/* "$installdir/shaders/" 2>/dev/null || true
  fi

  # Optional ROM-based menu, if it was built
  if [[ -f menu.bin ]]; then
    install -m 644 menu.bin "$installdir/menu.bin"
  fi

  install -d "$pkgdir/usr/bin"
  ln -s "/opt/blastem-anthonydotmoe/blastem" "$pkgdir/usr/bin/blastem"

  # License
  install -d "$pkgdir/usr/share/licenses/$pkgname"
  if [[ -f COPYING ]]; then
    install -m 644 COPYING "$pkgdir/usr/share/licenses/$pkgname/"
  fi
}
