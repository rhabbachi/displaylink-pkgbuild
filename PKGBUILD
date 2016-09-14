# Maintainer: PlusMinus

pkgname=displaylink
pkgver=1.1.62
pkgrel=2
evdiver=1.2.55
pkgdesc="DisplayLink DL-5xxx, DL-41xx and DL-3x00 Driver for Linux"
arch=('i686' 'x86_64')
url="http://www.displaylink.com/downloads/ubuntu.php"
license=('custom' 'GPL2' 'LGPL2.1')
depends=('dkms')
install=${pkgname}.install
changelog="release-note.txt"
source=(displaylink-driver-$pkgver.zip::http://www.displaylink.com/downloads/file\?id\=607
        99-displaylink.rules displaylink.service 
        displaylink-sleep.sh evdi-$pkgver-src.tar.gz::https://github.com/DisplayLink/evdi/archive/v$evdiver.tar.gz)
md5sums=('85879b750b26c464bd0f564fb76f398f'
         '37e076a16be49985f1d6800f960d16b4'
         'c141a15e973481c7d961f8e135627ca4'
         '4185b016cd64c6069302239515afadff'
         'SKIP')

package() {
  echo "Adding udev rule for DisplayLink DL-3xxx/5xxx devices"
  install -D -m644 99-displaylink.rules "$pkgdir/etc/udev/rules.d/99-displaylink.rules"

  echo "Installing DLM systemd service"
  install -D -m644 displaylink.service "$pkgdir/usr/lib/systemd/system/displaylink.service"
  install -D -m755 displaylink-sleep.sh "$pkgdir/usr/lib/systemd/system-sleep/displaylink.sh"
  
  COREDIR="$pkgdir/usr/lib/displaylink"
  install -d -m755 $COREDIR
  install -d -m750 "$pkgdir/var/log/displaylink"

  echo "Configuring EVDI DKMS module from https://github.com/DisplayLink/evdi"
  SRCDIR="$pkgdir/usr/src/evdi-$pkgver"
  mkdir -p $SRCDIR
  tar xf evdi-$pkgver-src.tar.gz
  cp -r evdi-$evdiver/module/* $SRCDIR
  echo "Making Installing EVDI DKMS libraries"
  cd evdi-$evdiver/library/
  make
  install -D -m755 libevdi.so $COREDIR/libevdi.so

  echo "Extracting DisplayLink Driver Package"
  cd $srcdir
  chmod +x displaylink-driver-$pkgver.run
  ./displaylink-driver-$pkgver.run --target $pkgname-$pkgver --noexec
  cd "$pkgname-$pkgver"
  
  if [ "$CARCH" == "i686" ]; then
    ARCH="x86"
  elif [ "$CARCH" == "x86_64" ]; then
    ARCH="x64"
  fi
  echo "Installing DisplayLink Manager $ARCH"
  install -D -m755 $ARCH/DisplayLinkManager $COREDIR/DisplayLinkManager

  echo "Installing libraries"
  install -D -m755 $ARCH/libusb-1.0.so.0.1.0 $COREDIR/libusb-1.0.so.0.1.0

  ln -s /usr/lib/displaylink/libusb-1.0.so.0.1.0 $COREDIR/libusb-1.0.so.0
  ln -s /usr/lib/displaylink/libusb-1.0.so.0.1.0 $COREDIR/libusb-1.0.so

  echo "Installing firmware packages"
  install -D -m644 *.spkg $COREDIR

  echo "Installing license file"
  install -D -m644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}

