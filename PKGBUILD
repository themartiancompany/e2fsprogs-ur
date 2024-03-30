# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Truocolo <truocolo@aol.com>
# # Maintainer: Ronald van Haren <ronald.archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

_systemd=true
_os="$( \
  uname \
  -o)"
echo \
  "os: ${_os}"
[[ "${_os}" == "Android"  ]] && \
  _systemd="false"
_pkg=e2fsprogs
pkgbase="${_pkg}"
pkgname=(
  'e2fsprogs'
  'fuse2fs'
)
pkgver=1.47.0
pkgrel=2
pkgdesc='Ext2/3/4 filesystem utilities'
arch=(
  'x86_64'
  'arm'
)
license=(
  'GPL'
  'LGPL'
  'MIT'
)
url="http://${_pkg}.sourceforge.net"
makedepends=(
  'util-linux'
  'fuse'
)
[[ "${_systemd}" == "true" ]] && \
  makedepends+=(
  )
validpgpkeys=(
   # Theodore Ts'o <tytso@mit.edu>
  '3AB057B7E78D945C8C5591FBD36F769BC11804F0'
  )
_kernel="https://www.kernel.org/pub/linux/kernel/people"
_ns="tytso"
source=(
  "${_kernel}/${_ns}/${_pkg}/v${pkgver}/${_pkg}-${pkgver}.tar."{xz,sign}
  'MIT-LICENSE'
)
sha256sums=(
  '144af53f2bbd921cef6f8bea88bb9faddca865da3fbc657cc9b4d2001097d5db'
  'SKIP'
  'cc45386c1d71f438ad648fd7971e49e3074ad9dbacf9dd3a5b4cb61fd294ecbb'
)

prepare() {
  cd \
    "${srcdir}/${pkgbase}-${pkgver}"
  # Remove unnecessary init.d directory
  sed \
    -i \
    '/init\.d/s|^|#|' \
    misc/Makefile.in
}

_bin="$( \
  dirname \
    "$( \
      command \
        -v \
	"cc" \
	"gcc" | \
      head \
        -n \
	1)")"

_usr="$( \
  dirname \
    "${_bin}")"

build() {
  local \
    _cflags=() \
    _ldflags=() \
    _configure_opts=()
  _configure_opts=(
    --prefix=/usr
    --with-root-prefix=''
    --libdir=/usr/lib
    --sbindir=/usr/bin
    --enable-elf-shlibs
    --disable-fsck
    --disable-uuidd
    --disable-libuuid
    --disable-libblkid
  )
  _cflags=(
    "${CFLAGS}"
    -I"${_usr}/include"
  )
  _ldflags=(
    "${LDFLAGS}"
    -L"${_usr}/include"
  )
  [[ "${_os}" == "Android" ]] && \
    echo \
      "enabling ${_os} flags" && \
    _cflags+=(
      -Wno-implicit-function-declaration
      # -Wno-error-implicit-function-declaration
    ) && \
    _configure_opts+=(
      --enable-symlink-install
      # --enable-relative-symlinks
      --enable-sym--link-build
      # --disable-rpath
      --disable-e2initrd-helper
      --disable-defrag
    )
  cd \
    "${srcdir}/${pkgbase}-${pkgver}"
  echo \
    "configure options:" \
    "${_configure_opts[@]}"
  LDFLAGS="${_cflags[*]}" \
  CFLAGS="${_cflags[*]}" \
  CXXFLAGS="${_cflags[*]}" \
  ./configure \
    "${_configure_opts[@]}"
  LDFLAGS="${_cflags[*]}" \
  CFLAGS="${_cflags[*]}" \
  CXXFLAGS="${_cflags[*]}" \
  make
  # regenerate locale files
  find \
    po \
    -name \
      '*.gmo' \
  CFLAGS="${_cflags[*]}" \
  CXXFLAGS="${_cflags[*]}" \
  LDFLAGS="${_ldflags[*]}" \
   -delete
  make \
    -C \
      po \
    update-gmo
}

package_e2fsprogs() {
  depends=(
    'sh'
    'util-linux-libs')
  optdepends=(
    'lvm2: for e2scrub'
    'util-linux: for e2scrub'
    'smtp-forwarder: for e2scrub_fail script'
  )
  provides=(
    'libcom_err.so'
    'libe2p.so'
    'libext2fs.so'
    'libss.so'
  )
  backup=(
    'etc/mke2fs.conf'
    'etc/e2scrub.conf'
  )
  unset \
    MAKEFLAGS
  cd \
    "${srcdir}/${pkgbase}-${pkgver}"
  make \
    DESTDIR="${pkgdir}" \
    PREFIX="/usr" \
      install \
      install-libs
  sed \
    -i \
    -e \
    's/^AWK=.*/AWK=awk/' \
    "${pkgdir}/usr/bin/compile_et"
  # remove references to build directory
  sed \
    -i \
    -e \
      's#^DIR=.*#DIR="/usr/share/ss"#' \
      "${pkgdir}/usr/bin/mk_cmds"
  sed \
    -i \
    -e \
      's#^DIR=.*#DIR="/usr/share/et"#' \
      "${pkgdir}/usr/bin/compile_et"
  # remove static libraries
  # with a shared counterpart
  rm \
  "${pkgdir}/usr/lib/lib"{com_err,e2p,ext2fs,ss}.a

  # remove fuse2fs which
  # will be packaged separately
  rm \
    "${pkgdir}/usr/"{bin/fuse2fs,share/man/man1/fuse2fs.1}
  # install MIT license
  install \
    -Dm0644 \
    "${srcdir}/MIT-LICENSE" \
    "${pkgdir}/usr/share/licenses/${pkgname}/MIT-LICENSE"
}

package_fuse2fs() {
  pkgdesc='Ext2/3/4 filesystem driver for FUSE'
  depends=(
    'fuse'
    'e2fsprogs'
  )
  cd \
    "${srcdir}/${pkgbase}-${pkgver}"
  install \
    -Dm0755 \
    'misc/fuse2fs' \
    "${pkgdir}/usr/bin/fuse2fs"
  install \
    -Dm0644 \
    'misc/fuse2fs.1' \
    "${pkgdir}/usr/share/man/man1/fuse2fs.1"
  mkdir \
    -p \
    "${pkgdir}"/usr/share/licenses
  ln \
    -s \
    "${pkgbase}" \
    "${pkgdir}/usr/share/licenses/${pkgname}"
}

# vim:set sw=2 sts=-1 et:
