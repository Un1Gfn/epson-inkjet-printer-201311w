# Maintainer: Andre Klitzing <andre () incubo () de>

pkgname=epson-inkjet-printer-201311w
pkgver=1.0.1

_suffix=1lsb3.2.src.rpm

_pkgnameF=epson-inkjet-printer-filter
_pkgverF=1.0.0

pkgrel=1
pkgdesc="Epson printer driver (L1300)"
arch=($CARCH)
url="http://download.ebz.epson.net/dsc/search/01/search/?OSC=LX"
license=(LGPL 'custom:Epson Licence Agreement')
depends=(cups ghostscript)
source=(
  ${pkgname}-${pkgver}-${_suffix}
  fixbuild.patch
)
sha256sums=(
  2d06e7afb14644b15c86e57434d37a3aec89c9c9477fd43dd098046bed35863b
  4936d365168c0a8519093e003b2dddc680399e14d3e6e0ea49b59617672323c3
)

build() {

  sha256sum -c <<<"df9dfae1ff1ab9dcfcf392a36a258971dae78c13d2fe706dce2cf43d12d78da6  $_pkgnameF-$_pkgverF.tar.gz"

  tar xf $_pkgnameF-$_pkgverF.tar.gz
  cd $_pkgnameF-$_pkgverF

  patch -p1 --verbose -i "$srcdir"/fixbuild.patch

  # autoupdate -vf
  autoreconf -vfi -Wall

  # if you have runtime problems: add "--enable-debug" and look into /tmp/epson-inkjet-printer-filter.txt
  export LDFLAGS="$LDFLAGS -Wl,--no-as-needed"
  ./configure  --prefix=/opt/$pkgname

  make

}

package() {

  # install filter (built from source)
  cd "$srcdir"/$_pkgnameF-$_pkgverF
  install -vDm755 src/epson_inkjet_printer_filter "$pkgdir/opt/$pkgname/cups/lib/filter/epson_inkjet_printer_filter"
  echo

  # extract driver (distributed in binary)
  cd "$srcdir"
  tar xf $pkgname-$pkgver.tar.gz
  echo

  # install driver (distributed in binary)
  cd $pkgname-$pkgver
  install -vdm755 "$pkgdir/opt/$pkgname/"
  case "$CARCH" in
    x86_64) cp -av --no-preserve=mode lib64 "$pkgdir"/opt/$pkgname/lib; ln -sfv lib "$pkgdir"/opt/$pkgname/lib64;;
    i686)   cp -av --no-preserve=mode lib   "$pkgdir"/opt/$pkgname/lib;;
    *)      echo "unsupported architecture '$CARCH'"; false;;
  esac
  cp -av --no-preserve=mode resource "$pkgdir"/opt/$pkgname/
  [ -e watermark ] && cp -av --no-preserve=mode watermark "$pkgdir/opt/$pkgname/"
  install -vdm755 "$pkgdir"/usr/share/cups/model/$pkgname
  install -vDm644 ppds/* "$pkgdir"/usr/share/cups/model/$pkgname/
  echo

}
