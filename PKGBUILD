# Maintainer: Alexander F. Rødseth <xyproto@archlinux.org>
# Contributor: Eli Schwartz <eschwartz@archlinux.org>
# Contributor: Lex Black <autumn-wind@web.de>
# Contributor: Michael Jakl <jakl.michael@gmail.com>
# Contributor: devmotion <nospam-archlinux.org@devmotion.de>
# Contributor: Valentin Churavy <v.churavy@gmail.com>

pkgbase=julia
pkgname=(julia julia-docs)
epoch=2
pkgver=1.2.0
pkgrel=1
arch=(x86_64)
pkgdesc='High-level, high-performance, dynamic programming language'
url='https://julialang.org/'
license=(MIT)
depends=(cblas fftw hicolor-icon-theme libgit2 libunwind libutf8proc openblas
         suitesparse)
makedepends=(cmake gcc-fortran gmp python2)
source=("https://github.com/JuliaLang/julia/releases/download/v$pkgver/$pkgbase-$pkgver-full.tar.gz"{,.asc}
        'cblas.patch::https://github.com/JuliaLang/julia/pull/29540/commits/0c442318196389d653ee21eba65d8c4f7beb72a0.patch'
        libunwind-version.patch
        makefile.patch
        Make.user)
sha256sums=('2419b268fc5c3666dd9aeb554815fe7cf9e0e7265bc9b94a43957c31a68d9184'
            'SKIP'
            '88fcbd8a2450027aada0892a60c49c891a8dae43ee6c19e64364b1a1373d50bc'
            'a5eec1e43e1161c313b1d32a5f35a67d6b4a2bbc2d6d324c010f6f2b35be4a72'
            'b7374fcd5a579fc59d6988795fc0c3cf411a89205942c691a5b3003793ae6c52'
            'SKIP')
# Julia (Binary signing key) <buildbot@julialang.org>
validpgpkeys=('3673DF529D9049477F76B37566E3C7DC03D6E495')

prepare() {
  cd $pkgbase-$pkgver

  # Add and use option to build with system cblas
  patch -p1 --no-backup-if-mismatch -i ../cblas.patch

  # Fixing libunwind version check
  # https://github.com/JuliaLang/julia/pull/29082
  patch -p1 -i ../libunwind-version.patch

  # Patching make install
  #patch -p0 -i ../makefile.patch

  # Configuring the build
  cp -f ../Make.user Make.user

  # Prepare a symlink from "python" to "python2"
  mkdir -p "$srcdir/bin"
  ln -s /usr/bin/python2 "$srcdir/bin/python"
}

build() {
  export PATH="$srcdir/bin:$PATH"
  env CFLAGS="$CFLAGS -w" CXXFLAGS="$CXXFLAGS -w" make -C $pkgbase-$pkgver
}

#check() {
# cd $pkgbase-$pkgver/test
#
# # this is the make testall target, plus the --skip option from
# # travis/appveyor/circleci (one test fails with DNS resolution errors)
# ../julia --check-bounds=yes --startup-file=no ./runtests.jl all --skip Sockets --skip Distributed
# find ../stdlib \( -name \*.cov -o -name \*.mem \) -delete
# rm -r depot/compiled
#}

package_julia() {
  backup=(etc/julia/startup.jl)
  optdepends=('gnuplot: If using the Gaston Package from julia')

  make -C $pkgbase-$pkgver DESTDIR="$pkgdir" install

  # Documentation is in the julia-docs package.
  # Man pages in /usr/share/julia/doc/man are duplicate.
  rm -rf "$pkgdir/usr/share/"{doc,julia/doc}

  install -Dm644 $pkgbase-$pkgver/LICENSE.md \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE.md"
}

package_julia-docs() {
  pkgdesc='Documentation and examples for Julia'
  depends=(julia)

  install -d "$pkgdir/usr/share/doc"
  cp -r $pkgbase-$pkgver/doc "$pkgdir/usr/share/doc/$pkgbase"
  rm -rf "$pkgdir/usr/share/doc/julia/man"
  install -Dm644 $pkgbase-$pkgver/LICENSE.md \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE.md"
}

# getver: julialang.org/downloads
# vim: ts=2 sw=2 et:
