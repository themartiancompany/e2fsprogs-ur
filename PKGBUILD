# Maintainer: 
# Contributor: judd <jvinet@zeroflux.org>

pkgname=e2fsprogs
pkgver=1.41.3
pkgrel=2
pkgdesc="Ext2/3/4 filesystem utilities"
arch=('i686' 'x86_64')
license=('GPL' 'LGPL' 'MIT')
url="http://e2fsprogs.sourceforge.net"
groups=('base')
depends=('glibc')
makedepends=('bc')
source=(http://downloads.sourceforge.net/sourceforge/${pkgname}/${pkgname}-${pkgver}.tar.gz
	'Makefile-fsck.static.patch')
install=$pkgname.install
md5sums=('b21d26fc46c584021dc9c444933ee1c2'
         'ce2b0daf84e17e8c7880ba3d43020ea3')

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
}
