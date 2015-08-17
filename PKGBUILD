# Maintainer: Gustavo Alvarez <sl1pkn07@gmail.com>
# Based On: Sergej Pupykin <pupykin.s+arch@gmail.com>

_patchlevel=1
_basever=0.148

pkgname=sdlmame-wout-gconf
_pkgname=sdlmame
pkgver="${_basever}.u${_patchlevel}"
pkgrel=1
pkgdesc="A port of the popular Multiple Arcade Machine Emulator using SDL with OpenGL support. Without Gconf and Gtk"
url="http://mamedev.org/"
license=('custom:MAME License')
arch=('i686' 'x86_64')
provides=('sdlmame')
conflicts=('sdlmame')
depends=('sdl>=1.2.11' 'fontconfig' 'sdl_ttf' 'alsa-lib')
makedepends=('nasm' 'mesa' 'glu' 'wget' 'python2')
optdepends=('sdlmamefamily-tools: Tools for manage MAME/MESS/UME roms')
DLAGENTS=('http::/usr/bin/wget -U "Mozilla/5.0 (X11; U; Linux x86_64; en-US; rv:1.9.1.2) Gecko/20090804 Shiretoko/3.5.2" -c -t 3 --waitretry=3 -O %o %u')
install="${_pkgname}.install"

for i in $(seq 1 "${_patchlevel}"); do
  _patches="${_patches} sdlmame-${_basever/./}u${i}_diff.zip::http://mamedev.org/updates/${_basever/./}u${i}_diff.zip"
done

source=("mame${_basever/./}s.zip::http://mamedev.org/downloader.php?file=releases/mame${_basever/./}s.zip"
        "sdlmame.sh"
        "extras.tar.gz"
        ${_patches}
        'sdlmame-wout-gconf-v3.patch')
md5sums=('38f7727c2961cd31e2ab6aa1814a23ba'
         'e11a7d26704a9e2cff223b9b867d6a05'
         '420b61240bf5ae11615ba7c6100ee00d'
         '01edd53824784f52448f4128f6d52aac'
         'd3d87fcab1b0629ba4bc56d305f5843a')

build() {
  cd "${srcdir}"
  bsdtar -xf mame.zip
  find . -type f -not -name "*.png" -exec perl -pi -e 's|\r\n?|\n|g' "{}" \;
  for i in $(seq 1 "${_patchlevel}"); do
    msg "Patch#${i}"
    patch --silent -p0 -E -i "${_basever/./}u${i}.diff"
  done

  msg "Patch disable Gconf and Gtk dependencies"
  patch --silent -p0 -E -i "${srcdir}/sdlmame-wout-gconf-v3.patch"
  msg2 "Done"

  make PYTHON=python2 NO_X11=1 OPTIMIZE=2 NOWERROR=1 ARCHOPTS=-march=native FULLNAME=mame
}

package() {
  cd "${srcdir}"

  # Install the sdlmame script
  install -Dm755 "${_pkgname}.sh" "${pkgdir}/usr/bin/${_pkgname}"

  # Install the applications and the UI font in /usr/share
  install -Dm755 mame "${pkgdir}/usr/share/${_pkgname}/${_pkgname}"

  # Install the extra bits
  install -d "${pkgdir}/usr/share/${_pkgname}/"{artwork,ctrlr,keymaps,shader}
  install -m644 artwork/* "${pkgdir}/usr/share/${_pkgname}/artwork/"
  install -m644 ctrlr/* "${pkgdir}/usr/share/${_pkgname}/ctrlr/"
  install -m644 src/osd/sdl/keymaps/* "${pkgdir}/usr/share/${_pkgname}/keymaps/"
  install -m644 src/osd/sdl/shader/glsl*.*h "${pkgdir}/usr/share/${_pkgname}/shader/"

  # Install man
  install -Dm644 src/osd/sdl/man/mame.6 "${pkgdir}/usr/share/man/man6/sdlmame.6"

  # Include the license
  install -Dm644 docs/license.txt "${pkgdir}/usr/share/licenses/${_pkgname}/license.txt"

  # FS#28203
  sed -e 's|KEYCODE_2_PAD|KEYCODE_2PAD|' -e 's|KEYCODE_4_PAD|KEYCODE_4PAD|' -e 's|KEYCODE_6_PAD|KEYCODE_6PAD|' -e 's|KEYCODE_8_PAD|KEYCODE_8PAD|' \
  -i "${pkgdir}/usr/share/sdlmame/ctrlr/"*.cfg
}
