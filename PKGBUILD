# Maintainer: 
# Contributor: judd <jvinet@zeroflux.org>

pkgname=e2fsprogs
pkgver=1.41.2
pkgrel=1
pkgdesc="Ext2/3/4 filesystem utilities"
arch=('i686' 'x86_64')
license=('GPL' 'LGPL' 'MIT')
url="http://e2fsprogs.sourceforge.net"
groups=('base')
depends=('glibc')
makedepends=('bc')
source=(http://downloads.sourceforge.net/sourceforge/${pkgname}/${pkgname}-${pkgver}.tar.gz
	Makefile-fsck.static.patch
	mke2fs.conf)
install=e2fsprogs.install
md5sums=('1c1c5177aea9a23b45b9b3f5b3241819'
         'ce2b0daf84e17e8c7880ba3d43020ea3'
         '4ebb30bdb5951f1fc86c1470e4d81532')

build() {
  cd ${srcdir}/${pkgname}-${pkgver}
  
  # Remove unnecessary init.d directory
  sed -i '/init\.d/s|^|#|' misc/Makefile.in
  ./configure --prefix=/usr --with-root-prefix="" --enable-elf-shlibs
  
  # add fsck.static for mkinitrd use
  patch -Np1 -i ../Makefile-fsck.static.patch || return 1
  
  make || return 1
  make DESTDIR=${pkgdir} install install-libs
  make -C ${srcdir}/${pkgname}-${pkgver}/misc fsck.static || return 1
  
  install -D -m755 ${srcdir}/${pkgname}-${pkgver}/misc/fsck.static \
	 ${pkgdir}/sbin/fsck.static
  
  sed -i -e 's/^AWK=.*/AWK=awk/' ${pkgdir}/usr/bin/compile_et || return 1
  
  install -m644 ${srcdir}/mke2fs.conf ${pkgdir}/etc/ || return 1
}
