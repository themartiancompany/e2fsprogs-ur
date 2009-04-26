# Maintainer: Ronald van Haren <ronald.archlinux.org> 
# Contributor: judd <jvinet@zeroflux.org>

pkgname=e2fsprogs
pkgver=1.41.5
pkgrel=2
pkgdesc="Ext2/3/4 filesystem utilities"
arch=('i686' 'x86_64')
license=('GPL' 'LGPL' 'MIT' 'BSD')
url="http://e2fsprogs.sourceforge.net"
groups=('base')
depends=('glibc' 'bash')
makedepends=('bc')
source=(http://downloads.sourceforge.net/sourceforge/${pkgname}/${pkgname}-${pkgver}.tar.gz
	'Makefile-fsck.static.patch' 'MIT-LICENSE')
backup=('etc/mke2fs.conf')
install=${pkgname}.install
md5sums=('e218df6c84fc17c1126d31de9472a76c'
         'ce2b0daf84e17e8c7880ba3d43020ea3'
         '035b7c69b7a2cecf996a4708c262245e')

build() {
  cd ${srcdir}/${pkgname}-${pkgver}
  
  # Remove unnecessary init.d directory
  sed -i '/init\.d/s|^|#|' misc/Makefile.in

  ./configure --prefix=/usr --with-root-prefix="" --enable-elf-shlibs
  
  # add fsck.static for mkinitrd use
  patch -Np1 -i ../Makefile-fsck.static.patch || return 1
  
  make || return 1
  make DESTDIR=${pkgdir} install install-libs || return 1
  make -C ${srcdir}/${pkgname}-${pkgver}/misc fsck.static || return 1
  
  install -D -m755 ${srcdir}/${pkgname}-${pkgver}/misc/fsck.static \
	 ${pkgdir}/sbin/fsck.static || return 1
  
  sed -i -e 's/^AWK=.*/AWK=awk/' ${pkgdir}/usr/bin/compile_et || return 1

  # install MIT and BSD licenses
  install -Dm644 ${srcdir}/MIT-LICENSE \
	${pkgdir}/usr/share/licenses/${pkgname}/MIT-LICENSE
  install -Dm644 ${srcdir}/${pkgname}-${pkgver}/lib/uuid/COPYING \
	${pkgdir}/usr/share/licenses/${pkgname}/BSD-LICENSE
}
