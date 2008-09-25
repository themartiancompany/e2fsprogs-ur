# Maintainer: 
# Contributor: judd <jvinet@zeroflux.org>

pkgname=e2fsprogs
pkgver=1.41.1
pkgrel=1
pkgdesc="Ext2 filesystem utilities"
arch=('i686' 'x86_64')
license=('GPL' 'LGPL' 'MIT')
url="http://e2fsprogs.sourceforge.net"
groups=('base')
depends=('glibc')
makedepends=('bc')
source=(http://downloads.sourceforge.net/sourceforge/${pkgname}/${pkgname}-${pkgver}.tar.gz
	Makefile-fsck.static.patch
	mke2fs.conf)
md5sums=('ddf13b3c9ece247c457db20ca9a10c3c'
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
  make DESTDIR=${startdir}/pkg install install-libs
  make -C ${startdir}/src/${pkgname}-${pkgver}/misc fsck.static || return 1
  
  install -D -m755 ${srcdir}/${pkgname}-${pkgver}/misc/fsck.static \
	 ${pkgdir}/sbin/fsck.static
  
  sed -i -e 's/^AWK=.*/AWK=awk/' ${pkgdir}/usr/bin/compile_et || return 1
  
  install -m644 ${startdir}/src/mke2fs.conf ${pkgdir}/etc/ || return 1
}
