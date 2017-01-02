# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=1.9.1
pkgrel=1
pkgdesc="A tool for securing communications between a client and a DNS resolver"
arch=('i686' 'x86_64')
url="http://dnscrypt.org/"
license=('custom:ISC')
depends=('libsodium' 'systemd' 'libtool' 'ldns')
install=dnscrypt-proxy.install
backup=('etc/dnscrypt-proxy.conf')
source=("http://download.dnscrypt.org/$pkgname/$pkgname-$pkgver.tar.gz")
sha512sums=('94f1f11d9b7366a5fa12bf9368478de8a666e82836c82b48310b4d93b6aa5a6f63085192ab3c3ab0afcc7452ae2ac81620b5d8b95e5937b94b6ac609a6e92570')

prepare() {
  cd $pkgname-$pkgver

  sed -e '/--resolver-name/d' \
      -e '/--user/d' \
      -e 's|^ExecStart=.*|ExecStart=/usr/bin/dnscrypt-proxy /etc/dnscrypt-proxy.conf|' \
      -i dnscrypt-proxy.service
}

build() {
  cd $pkgname-$pkgver

  ./configure --prefix=/usr --sbindir=/usr/bin --sysconfdir=/etc --with-systemd
  make -j2
}

package() {
  cd $pkgname-$pkgver
  make DESTDIR="$pkgdir" install

  mkdir -p "$pkgdir"/{usr/share/{licenses,doc}/$pkgname,usr/lib/systemd/system}
  install -m 644 COPYING "$pkgdir"/usr/share/licenses/$pkgname
  install -m 644 AUTHORS NEWS README README.markdown "$pkgdir"/usr/share/doc/$pkgname
  install -m 644 dnscrypt-proxy.service "$pkgdir"/usr/lib/systemd/system
  install -m 644 dnscrypt-proxy.socket "$pkgdir"/usr/lib/systemd/system
}
