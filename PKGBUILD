# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Giuseppe Borzi <gborzi _AT_ ieee _DOT_ org>

pkgname=openblas
_pkgname=OpenBLAS
pkgver=0.3.2
pkgrel=1
pkgdesc="An optimized BLAS library based on GotoBLAS2 1.13 BSD"
arch=('x86_64')
url="http://www.openblas.net/"
license=('BSD')
depends=('gcc-libs')
makedepends=('perl' 'gcc-fortran')
provides=('blas=3.8.0')
conflicts=('blas')
source=(${_pkgname}-v${pkgver}.tar.gz::https://github.com/xianyi/OpenBLAS/archive/v${pkgver}.tar.gz)
sha512sums=('13bc2aae763dadfe3ded33d12eb56fa3a042007047e9e84f6d18ed576394a45590f9f248d72c954c90cd2612e5cb0e1238a8bf83520160467fa720cf89ddb101')

build() {
  cd "$srcdir/$_pkgname-$pkgver"

  make NO_STATIC=1 NO_LAPACK=1 NO_LAPACKE=1 NO_CBLAS=1 NO_AFFINITY=1 USE_OPENMP=1 \
       CFLAGS="$CPPFLAGS $CFLAGS" DYNAMIC_ARCH=1 \
       NUM_THREADS=64 MAJOR_VERSION=3 libs shared
}

package() {
  cd "$srcdir/$_pkgname-$pkgver"

  make PREFIX="$pkgdir/usr" NUM_THREADS=64 MAJOR_VERSION=3 install
  rm -f "$pkgdir/usr/include/cblas.h" "$pkgdir"/usr/include/lapacke*
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"

  cd "$pkgdir/usr/lib/"
  ln -sf libopenblasp-r$pkgver.so libblas.so
  ln -sf libopenblasp-r$pkgver.so libblas.so.3
  sed -i -e "s%$pkgdir%%" "$pkgdir/usr/lib/cmake/openblas/OpenBLASConfig.cmake"
  sed -i -e "s%$pkgdir%%" "$pkgdir/usr/lib/pkgconfig/openblas.pc"

  rmdir "$pkgdir"/usr/bin
}

# vim:set ts=2 sw=2 et:
