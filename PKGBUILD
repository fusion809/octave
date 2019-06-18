# Maintainer: Ronald van Haren <ronald.archlinux.org>
# Contributor : shining <shiningxc.at.gmail.com>
# Contributor : cyberdune <cyberdune@gmail.com>

pkgname=octave
pkgver=5.1.0
pkgrel=5
pkgdesc="A high-level language, primarily intended for numerical computations."
arch=('x86_64')
url="http://www.octave.org"
license=('GPL')
depends=('fftw' 'curl' 'graphicsmagick' 'glpk' 'hdf5' 'qhull' 'arpack' 'glu' 'ghostscript'
 	   'suitesparse' 'gl2ps' 'qscintilla-qt5' 'libsndfile' 'qt5-tools' 'qrupdate')
makedepends=('gcc-fortran' 'texlive-core' 'suitesparse' 'texinfo' 'gnuplot' 'fltk' 'portaudio' 'jdk8-openjdk' 'sundials')
optdepends=('texinfo: for help-support in octave'
            'gnuplot: alternative plotting'
            'portaudio: audio support'
            'java-runtime: java support'
            'sundials: ode15i and ode15s solvers'
            'fltk: FLTK GUI')
source=(ftp://ftp.gnu.org/gnu/octave/octave-$pkgver.tar.gz{,.sig}
gnu-octave
octave-sundials4.patch)
options=('!emptydirs')
validpgpkeys=('DBD9C84E39FE1AAE99F04446B05F05B75D36644B')  # John W. Eaton
sha512sums=('a842fb50c13f25e9d425fe9a2c71d9433d7e125747d2175efe0c9b2a780c799d9ce1ee085b5a13fbfedb7990b0ba5d11079d880ddb3bdb66782efc321390eebb'
            'SKIP'
            '4b5bdeb0198463ae12b93f8018d642b9e67c623687e8e20f49da7c95a9c8165270a92dd3a808113ddc3ded18e206fc90988b6e2f5d61da87f3da3f95efc37d33'
            '4b743602e8ca91e8be8dab69e09d3e476e9edd867b2eb0b9816fbe4ca344a16bff7a413c2e89b0c9fb769f4a815a696c4d67b70282b7e4fe8c24598bcce90d34')

prepare() {
  cd $pkgname-$pkgver
  patch -p1 -i ../octave-sundials4.patch # Fix sundials support https://savannah.gnu.org/bugs/?52475
  autoreconf -vif
}

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  export QCOLLECTIONGENERATOR=qhelpgenerator # Fix build with Qt 5.12
  ./configure --prefix=/usr --libexecdir=/usr/lib \
    --enable-shared --disable-static \
    --with-quantum-depth=16 \
    --with-sundials_ida="-lsundials_ida -lsundials_sunlinsolklu"
# https://mailman.cae.wisc.edu/pipermail/help-octave/2012-September/053991.html 

  LANG=C make
}

package(){
  cd "${srcdir}/${pkgname}-${pkgver}"

  make DESTDIR="${pkgdir}" install

  # add octave library path to ld.so.conf.d
  install -d "${pkgdir}/etc/ld.so.conf.d"
  echo "/usr/lib/${pkgname}/${pkgver}" > "${pkgdir}/etc/ld.so.conf.d/${pkgname}.conf"
  install -Dm755 $srcdir/gnu-octave $pkgdir/usr/bin/gnu-octave
  sed -i -e "s|/usr/bin/octave|/usr/bin/gnu-octave|g" $pkgdir/usr/share/applications/org.octave.Octave.desktop
}
