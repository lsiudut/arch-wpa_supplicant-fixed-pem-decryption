# $Id$
# Maintainer:  Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Thomas Bächler <thomas@archlinux.org>

pkgname=wpa_supplicant
pkgver=2.6
pkgrel=5
epoch=1
pkgdesc="A utility providing key negotiation for WPA wireless networks"
url="http://hostap.epitest.fi/wpa_supplicant"
arch=('i686' 'x86_64')
depends=('openssl' 'libdbus' 'readline' 'libnl')
optdepends=('wpa_supplicant_gui: wpa_gui program')
license=('GPL')
install=wpa_supplicant.install
source=(https://w1.fi/releases/${pkgname}-${pkgver}.tar.gz{,.asc}
        config
        fix-pem-decryption.patch)
validpgpkeys=('EC4AA0A991A5F2464582D52D2B6EF432EFC895FA') # Jouni Malinen
sha256sums=('b4936d34c4e6cdd44954beba74296d964bc2c9668ecaa5255e499636fe2b1450'
            'SKIP'
            'e7724868cda101f94962dd247c859cab581bc51080ae967b9e6409502d5193d4'
            'eb229db751dca796f85961db3d8a6309ace35161935e11feceaaf3ed4b04896c')

prepare() {
  cd "$srcdir/$pkgname-$pkgver"
  patch -p0 < $srcdir/fix-pem-decryption.patch
  cd "$srcdir/$pkgname-$pkgver/$pkgname"
  cp "$srcdir/config" ./.config
}

build() {
  cd "$srcdir/$pkgname-$pkgver/$pkgname"

  # The Makefile does not pick up our CPPFLAGS
  export CFLAGS="$CPPFLAGS $CFLAGS"
  make LIBDIR=/usr/lib BINDIR=/usr/bin
  make LIBDIR=/usr/lib BINDIR=/usr/bin eapol_test
}

package() {
  cd "$srcdir/$pkgname-$pkgver/$pkgname"
  make LIBDIR=/usr/lib BINDIR=/usr/bin DESTDIR="$pkgdir" install
  install -Dm755 eapol_test "$pkgdir/usr/bin/eapol_test"

  install -d -m755 "$pkgdir/etc/wpa_supplicant"
  install -Dm644 wpa_supplicant.conf \
    "$pkgdir/usr/share/doc/wpa_supplicant/wpa_supplicant.conf"

  install -d -m755 "$pkgdir/usr/share/man/man"{5,8}
  install -m644 doc/docbook/*.5 "$pkgdir/usr/share/man/man5/"
  install -m644 doc/docbook/*.8 "$pkgdir/usr/share/man/man8/"
  rm -f "$pkgdir/usr/share/man/man8/wpa_"{priv,gui}.8

  install -d -m755 "$pkgdir/usr/share/dbus-1/system-services"
  install -m644 \
    dbus/fi.{epitest.hostap.WPASupplicant,w1.wpa_supplicant1}.service \
    "$pkgdir/usr/share/dbus-1/system-services/"

  install -Dm644 dbus/dbus-wpa_supplicant.conf \
    "$pkgdir/etc/dbus-1/system.d/wpa_supplicant.conf"

  install -d -m755 "$pkgdir/usr/lib/systemd/system"
  install -m644 systemd/*.service "$pkgdir/usr/lib/systemd/system/"
}
