# Maintainer: Ronald van Haren <ronald.archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=e2fsprogs
pkgver=1.46.3
pkgrel=2
pkgdesc="Ext2/3/4 filesystem utilities"
arch=('x86_64')
license=('GPL' 'LGPL' 'MIT')
url="http://e2fsprogs.sourceforge.net"
depends=('sh' 'util-linux-libs')
makedepends=('systemd' 'util-linux')
provides=('libcom_err.so'
          'libe2p.so'
          'libext2fs.so'
          'libss.so')
backup=('etc/mke2fs.conf')
options=('staticlibs')
validpgpkeys=('3AB057B7E78D945C8C5591FBD36F769BC11804F0') # Theodore Ts'o <tytso@mit.edu>
source=("https://www.kernel.org/pub/linux/kernel/people/tytso/${pkgname}/v${pkgver}/${pkgname}-${pkgver}.tar."{xz,sign}
        '0001-mke2fs-do-not-warn-about-a-pre-existing-partition-table-when-using-a-non-zero-offset.patch'
        'MIT-LICENSE')
sha256sums=('86d1580facdd49f2e0e6b027e26b1e6c48af538762dc40aeed2a87153c1f11b7'
            'SKIP'
            '48ac4367d729aede1ba84974a237e5c3421ea9b4371b17a83842b54b62931a80'
            'cc45386c1d71f438ad648fd7971e49e3074ad9dbacf9dd3a5b4cb61fd294ecbb')


prepare() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # revert: mke2fs: do not warn about a pre-existing partition table when using a non-zero offset
  patch -Np1 -R < ../0001-mke2fs-do-not-warn-about-a-pre-existing-partition-table-when-using-a-non-zero-offset.patch

  # Remove unnecessary init.d directory
  sed -i '/init\.d/s|^|#|' misc/Makefile.in
}

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  ./configure \
      --prefix=/usr \
      --with-root-prefix="" \
      --libdir=/usr/lib \
      --sbindir=/usr/bin \
      --enable-elf-shlibs \
      --disable-fsck \
      --disable-uuidd \
      --disable-libuuid \
      --disable-libblkid

  make

  # regenerate locale files
  find po/ -name '*.gmo' -delete
  make -C po update-gmo
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
