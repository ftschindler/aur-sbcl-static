# Maintainer: Felix Schindler <aut dot felixschindler dot net>
# Contributor: John Proctor <jproctor@prium.net>
# Contributor: Daniel White <daniel@whitehouse.id.au>
# Contributor: Juergen Hoetzel <juergen@archlinux.org>
# Contributor: Leslie Polzer (skypher)

_pkgname=sbcl
pkgname="${_pkgname}"-static
pkgver=2.2.0
pkgrel=1
pkgdesc="Steel Bank Common Lisp (patched to create static executables)"
url="http://www.sbcl.org/"
arch=('x86_64')
license=('custom')
depends=('zlib')
provides=("common-lisp=${pkgver}" "cl-asdf=${pkgver}" "${_pkgname}=${pkgver}")
conflicts=('sbcl')
makedepends=('sbcl')
source=("git+https://github.com/daewok/sbcl.git#branch=static-executable-v2-2.2.0"
        "arch-fixes.lisp")
sha256sums=('SKIP'
            'b5a6468dcbc1012cae2c3cda155762a37b6d96ef89bba4f723315063b0b5e7ce')


build() {
  cd "$srcdir/$_pkgname"
  export CFLAGS+=" -D_GNU_SOURCE -fno-omit-frame-pointer -DSBCL_HOME=/usr/lib/sbcl"
  export GNUMAKE="make"

  # build system uses LINKFLAGS and OS_LIBS to build LDFLAGS
  export LINKFLAGS="$LDFLAGS"
  unset LDFLAGS
  unset MAKEFLAGS
  sh make.sh sbcl --prefix=/usr --fancy
  make -C doc/manual info
}

package() {
  cd "$srcdir/$_pkgname"

  # cannot have both SBCL_HOME and INSTALL_ROOT
  SBCL_HOME="" INSTALL_ROOT="$pkgdir/usr" sh install.sh

  src/runtime/sbcl --core output/sbcl.core --script "${srcdir}/arch-fixes.lisp"
  mv sbcl-new.core "${pkgdir}/usr/lib/sbcl/sbcl.core"

  # sources
  mkdir -p "$pkgdir/usr/share/sbcl-source"
  cp -R -t "$pkgdir/usr/share/sbcl-source" "$srcdir/$_pkgname/"{src,contrib}

  # license
  install -D -m644 "$srcdir/$_pkgname/COPYING" \
                   "$pkgdir/usr/share/licenses/$pkgname/license.txt"

  # drop unwanted files
  find "$pkgdir" \( -name Makefile -o -name .cvsignore \) -delete
  find "$pkgdir/usr/share/sbcl-source" -type f \
    \( -name \*.fasl -o -name \*.o -o -name \*.log -o -name \*.so -o -name a.out \) -delete

  rm "$pkgdir/usr/share/sbcl-source/src/runtime/sbcl"

}
