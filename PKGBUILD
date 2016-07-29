# Maintainer: Ronald van Haren <ronald.archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=e2fsprogs
pkgver=1.43.1
pkgrel=2
pkgdesc="Ext2/3/4 filesystem utilities"
arch=('i686' 'x86_64')
license=('GPL' 'LGPL' 'MIT')
url="http://e2fsprogs.sourceforge.net"
groups=('base')
depends=('sh' 'libutil-linux')
makedepends=('bc' 'util-linux')
source=("http://downloads.sourceforge.net/sourceforge/${pkgname}/${pkgname}-${pkgver}.tar.gz"
        'MIT-LICENSE')
backup=('etc/mke2fs.conf')
options=('staticlibs')
sha1sums=('26b75c27ba434e72ef630b160782a01b4d992b7a'
          'f4a0d5b0cdb980e3fedd6f5e7dde0b0ffb7bbdfb')


prepare() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # Remove unnecessary init.d directory
  sed -i '/init\.d/s|^|#|' misc/Makefile.in
}

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  ./configure --prefix=/usr --with-root-prefix="" --libdir=/usr/lib \
      --sbindir=/usr/bin --enable-elf-shlibs --disable-fsck --disable-uuidd \
      --disable-libuuid --disable-libblkid

  make
}

package() {
  unset MAKEFLAGS

  cd "${srcdir}/${pkgname}-${pkgver}"
  make DESTDIR="${pkgdir}" install install-libs

  sed -i -e 's/^AWK=.*/AWK=awk/' "${pkgdir}/usr/bin/compile_et"

  # remove references to build directory
  sed -i -e 's#^SS_DIR=.*#SS_DIR="/usr/share/ss"#' "${pkgdir}/usr/bin/mk_cmds"
  sed -i -e 's#^ET_DIR=.*#ET_DIR="/usr/share/et"#' "${pkgdir}/usr/bin/compile_et"

  # remove static libraries with a shared counterpart
  rm "${pkgdir}"/usr/lib/lib{com_err,e2p,ext2fs,ss}.a

  # install MIT license
  install -Dm644 "${srcdir}/MIT-LICENSE" \
    "${pkgdir}/usr/share/licenses/${pkgname}/MIT-LICENSE"
}
