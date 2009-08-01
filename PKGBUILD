# Maintainer: Ronald van Haren <ronald.archlinux.org> 
# Contributor: judd <jvinet@zeroflux.org>

pkgname=e2fsprogs
pkgver=1.41.8
pkgrel=2
pkgdesc="Ext2/3/4 filesystem utilities"
arch=('i686' 'x86_64')
license=('GPL' 'LGPL' 'MIT')
url="http://e2fsprogs.sourceforge.net"
groups=('base')
depends=('sh' 'util-linux-ng>=2.16')
makedepends=('bc')
source=(http://downloads.sourceforge.net/sourceforge/${pkgname}/${pkgname}-${pkgver}.tar.gz
        MIT-LICENSE)
backup=('etc/mke2fs.conf')
install=${pkgname}.install
md5sums=('6708cc8e484809fc5cfb232882e48489'
         '035b7c69b7a2cecf996a4708c262245e')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"
  
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
