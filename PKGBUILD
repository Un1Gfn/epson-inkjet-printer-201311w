# Maintainer: Andre Klitzing <andre () incubo () de>
# Contributor: Darren Ng <$(base64 --decode <<<VW4xR2ZuQGdtYWlsLmNvbQo=)>

# driver (built from source)
_Dver=1.0.1

# filter (distributed in binary)
_F=epson-inkjet-printer-filter
_Fver=1.0.0
_Fs=df9dfae1ff1ab9dcfcf392a36a258971dae78c13d2fe706dce2cf43d12d78da6

pkgname=epson-inkjet-printer-201311w
pkgver=20150831
pkgrel=1
epoch=1
pkgdesc="Epson printer driver (L1300)"
arch=($CARCH)
url="http://download.ebz.epson.net/dsc/search/01/search/?OSC=LX"
license=(LGPL 'custom:Epson Licence Agreement')
depends=(cups ghostscript)
source=(
  ${pkgname}-${_Dver}-1lsb3.2.src.rpm
  fixbuild.patch
)
sha256sums=(
  2d06e7afb14644b15c86e57434d37a3aec89c9c9477fd43dd098046bed35863b
  4936d365168c0a8519093e003b2dddc680399e14d3e6e0ea49b59617672323c3
)

build() {

  sha256sum -c <<<"$_Fs  $_F-$_Fver.tar.gz"

  tar xf $_F-$_Fver.tar.gz
  cd $_F-$_Fver

  patch -p1 --verbose -i "$srcdir"/fixbuild.patch

  # autoupdate -vf
  autoreconf -vfi -Wall

  # if you have runtime problems: add "--enable-debug" and look into /tmp/epson-inkjet-printer-filter.txt
  export LDFLAGS="$LDFLAGS -Wl,--no-as-needed"
  ./configure  --prefix=/opt/$pkgname

  make

}

package() {

  # create directory in /opt
  _o="$pkgdir/opt/$pkgname"
  install -vdm755 "$_o"

  # install filter (built from source)
  install -vDm755 "$srcdir/$_F-$_Fver"/src/epson_inkjet_printer_filter \
                     "$_o"/cups/lib/filter/epson_inkjet_printer_filter
  echo

  # extract driver (distributed in binary)
  cd "$srcdir"
  tar xf "$pkgname-$_Dver.tar.gz"
  echo

  # install driver (distributed in binary)
  cd "$srcdir/$pkgname-$_Dver"
  case "$CARCH" in
    x86_64) cp -av --no-preserve=mode lib64 "$_o/lib"; ln -sv lib "$_o/lib64";;
    i686)   cp -av --no-preserve=mode lib   "$_o/lib";;
    *)      echo "unsupported architecture '$CARCH'"; return 1;;
  esac
  cp -av --no-preserve=mode resource "$pkgdir"/opt/$pkgname/
  [ -e watermark ] && cp -av --no-preserve=mode watermark "$_o"
  install -vdm755 "$pkgdir"/usr/share/cups/model/$pkgname
  install -vDm644 ppds/* "$pkgdir"/usr/share/cups/model/$pkgname/
  echo

}
