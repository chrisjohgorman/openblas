# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Giuseppe Borzi <gborzi _AT_ ieee _DOT_ org>

pkgbase=openblas
pkgname=(openblas openblas64 blas-openblas blas64-openblas)
_pkgname=OpenBLAS
pkgver=0.3.24
pkgrel=2
_blasver=3.11.0
pkgdesc="An optimized BLAS library based on GotoBLAS2 1.13 BSD"
arch=('x86_64')
url="https://www.openblas.net/"
license=('BSD')
depends=('gcc-libs')
makedepends=('cmake' 'perl' 'gcc-fortran')
source=(${_pkgname}-v${pkgver}.tar.gz::https://github.com/xianyi/OpenBLAS/archive/v${pkgver}.tar.gz)
sha512sums=('fe66e3a258ca1720764ed243f6d61017d6ef14bd33b76f20b19b34754096ec2be9fbeb1a78743f38ee71381746d6af9a1c16a8f3982e423afec422fcb50852d0')

build() {
  # Setting FC manually to avoid picking up f95 and breaking the cmake build
  # https://github.com/xianyi/OpenBLAS/issues/4072#issuecomment-1576388332

  FC=gfortran cmake -B build -S $_pkgname-$pkgver \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DBUILD_SHARED_LIBS=ON \
    -DBUILD_TESTING=OFF \
    -DNO_AFFINITY=ON \
    -DUSE_OPENMP=1 \
    -DNO_WARMUP=1 \
    -DCORE=CORE2 \
    -DNUM_THREADS=64 \
    -DDYNAMIC_ARCH=ON
  cmake --build build

  FC=gfortran cmake -B build64 -S $_pkgname-$pkgver \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DBUILD_SHARED_LIBS=ON \
    -DBUILD_TESTING=OFF \
    -DNO_AFFINITY=ON \
    -DUSE_OPENMP=1 \
    -DNO_WARMUP=1 \
    -DCORE=CORE2 \
    -DNUM_THREADS=64 \
    -DDYNAMIC_ARCH=ON \
    -DINTERFACE64=1
  cmake --build build64
}

check() {
  cd "$srcdir"/build
  ctest

  cd "$srcdir"/build64
  ctest
}

package_openblas() {
  DESTDIR="$pkgdir" cmake --install build
  install -Dm644 $_pkgname-$pkgver/LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}

package_openblas64() {
  pkgdesc+=" (64-bit integers)"
  DESTDIR="$pkgdir" cmake --install build64
  install -Dm644 $_pkgname-$pkgver/LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
 
  cd "$pkgdir"/usr/lib/
  ln -s libopenblas_64.so.${pkgver%.*} libopenblas64_.so # Needed by julia
}

package_blas-openblas() {
  pkgdesc+=" (Provides BLAS/CBLAS/LAPACK/LAPACKE system-wide)"
  depends=('openblas')
  provides=("blas=$_blasver" "cblas=$_blasver" "lapack=$_blasver" "lapacke=$_blasver" "openblas-lapack=$pkgver")
  conflicts=('blas' 'cblas' 'lapack' 'lapacke' 'openblas-lapack')
  replaces=('openblas-lapack')

  mkdir -p "$pkgdir"/usr/lib/pkgconfig
  cd "$pkgdir"/usr/lib/
  for _lib in blas cblas lapack lapacke; do
    ln -s libopenblas.so.${pkgver%.*} lib${_lib}.so
    ln -s libopenblas.so.${pkgver%.*} lib${_lib}.so.3
    ln -s openblas.pc "$pkgdir"/usr/lib/pkgconfig/${_lib}.pc
  done
}

package_blas64-openblas() {
  pkgdesc+=" (64-bit integers, provides BLAS/CBLAS/LAPACK/LAPACKE system-wide)"
  depends=('openblas64')
  provides=("blas64=$_blasver" "cblas64=$_blasver" "lapack64=$_blasver" "lapacke64=$_blasver")
  conflicts=('blas64' 'cblas64' 'lapack64' 'lapacke64')

  mkdir -p "$pkgdir"/usr/lib/pkgconfig
  cd "$pkgdir"/usr/lib/
  for _lib in blas64 cblas64 lapack64 lapacke64; do
    ln -s libopenblas_64.so.${pkgver%.*} lib${_lib}.so
    ln -s libopenblas_64.so.${pkgver%.*} lib${_lib}.so.3
    ln -s openblas64.pc "$pkgdir"/usr/lib/pkgconfig/${_lib}.pc
  done
}

# vim:set ts=2 sw=2 et:
