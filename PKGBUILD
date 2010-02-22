# Maintainer: Ronald van Haren <ronald.archlinux.org> 
# Contributor: judd <jvinet@zeroflux.org>

pkgname=e2fsprogs
pkgver=1.41.10
pkgrel=1
pkgdesc="Ext2/3/4 filesystem utilities"
arch=('i686' 'x86_64')
license=('GPL' 'LGPL' 'MIT')
url="http://e2fsprogs.sourceforge.net"
groups=('base')
depends=('sh' 'util-linux-ng>=2.16')
makedepends=('bc')
source=("http://downloads.sourceforge.net/sourceforge/${pkgname}/${pkgname}-${pkgver}.tar.gz"
        'MIT-LICENSE' '0001-implement-com_right_r.patch')
backup=('etc/mke2fs.conf')
install=${pkgname}.install
md5sums=('f9c7bb5c036a119453ce02fa871038da'
         '035b7c69b7a2cecf996a4708c262245e'
         '20bf4f686512fa0327ca17f61db226a6')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # provide heimdal's com_right_r() directly in e2fsprogs com_err: FS#18043
  patch -Np1 -i ${srcdir}/0001-implement-com_right_r.patch || return 1
  
  # Remove unnecessary init.d directory
  sed -i '/init\.d/s|^|#|' misc/Makefile.in || return 1

  ./configure --prefix=/usr --with-root-prefix="" --enable-elf-shlibs \
      --disable-fsck --disable-uuidd \
      --disable-libuuid --disable-libblkid || return 1


  make || return 1
  make DESTDIR="${pkgdir}" install install-libs || return 1
  
  sed -i -e 's/^AWK=.*/AWK=awk/' "${pkgdir}/usr/bin/compile_et" || return 1

  # install MIT license
  install -Dm644 "${srcdir}/MIT-LICENSE" \
    "${pkgdir}/usr/share/licenses/${pkgname}/MIT-LICENSE" || return 1
}
