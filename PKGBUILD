# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=1.9.0
pkgrel=1
pkgdesc="A tool for securing communications between a client and a DNS resolver"
arch=('i686' 'x86_64')
url="http://dnscrypt.org/"
license=('custom:ISC')
depends=('libsodium' 'systemd' 'libtool' 'ldns')
install=dnscrypt-proxy.install
backup=('etc/dnscrypt-proxy.conf')
source=("http://download.dnscrypt.org/$pkgname/$pkgname-$pkgver.tar.gz")
sha512sums=('62623c9f10d4d0b459a4f4efc85231f3683cf2379dc32fcbc5c4c691daa5050924bf65e1ef4782848f8698f76141272aed7221c6cdd13884d6d25bec264c4b7c')

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
