# Maintainer: drakkan <nicola.murino at gmail dot com>
# Contributor: Filip Brcic <brcha@gna.org>
# Contributor: ant32 <antreimer@gmail.com>
# Contributor: Renato Silva <br.renatosilva@gmail.com>
# Contributor: Martchus <martchus@gmx.net>
pkgname=mingw-w64-glib2
pkgver=2.80.3
pkgrel=1
arch=(any)
pkgdesc="Low level core library (mingw-w64)"
depends=(mingw-w64-libffi mingw-w64-pcre2 mingw-w64-gettext mingw-w64-zlib)
makedepends=(mingw-w64-meson python-packaging)
license=("LGPL2.1")
options=(!strip !buildflags staticlibs !emptydirs)
url="https://wiki.gnome.org/Projects/GLib"
source=("https://download.gnome.org/sources/glib/${pkgver%.*}/glib-${pkgver}.tar.xz"
  "0001-Use-CreateFile-on-Win32-to-make-sure-g_unlink-always.patch")
sha256sums=('3947a0eaddd0f3613d0230bb246d0c69e46142c19022f5c4b1b2e3cba236d417'
            '56d41de64a30099c42ecd47ed576fcd169548912619c10deb4a712b583548d30')

_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

prepare() {
  cd glib-${pkgver}
  # https://gitlab.gnome.org/GNOME/glib/issues/539
  patch -Np1 -i ../0001-Use-CreateFile-on-Win32-to-make-sure-g_unlink-always.patch
}


build() {
  for _arch in ${_architectures}; do
    mkdir -p "${srcdir}/glib-${pkgver}/build-${_arch}-static"
    mkdir -p "${srcdir}/glib-${pkgver}/build-${_arch}-shared"

    cd "${srcdir}/glib-${pkgver}/build-${_arch}-static"
    ${_arch}-meson \
      -D strip=true \
      --default-library static ..
    ninja

    cd "${srcdir}/glib-${pkgver}/build-${_arch}-shared"
    ${_arch}-meson \
      -D strip=true \
      --default-library shared ..
    ninja
  done
}

package() {
  for _arch in ${_architectures}; do
    # fix pkg-config files (see https://github.com/mesonbuild/meson/pull/3939)
    for pc_file in ${srcdir}/glib-${pkgver}/build-${_arch}-shared/meson-private/*.pc; do
      sed -i 's/-lgnulib//g' "$pc_file"
      sed -i 's/-lcharset//g' "$pc_file"
      sed -i 's/-lgiowin32//g' "$pc_file"
    done
    
    DESTDIR="${pkgdir}" ninja -C "${srcdir}/glib-${pkgver}/build-${_arch}-static" install
    DESTDIR="${pkgdir}" ninja -C "${srcdir}/glib-${pkgver}/build-${_arch}-shared" install
    if [[ $NO_EXECUTABLES ]]; then
      find "${pkgdir}/usr/${_arch}" -name '*.exe' -delete
    fi

  done
}

# vim: ts=2 sw=2 et:
