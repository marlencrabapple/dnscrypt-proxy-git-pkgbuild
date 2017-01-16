# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=1.9.2
pkgrel=1
pkgdesc="A tool for securing communications between a client and a DNS resolver"
arch=('i686' 'x86_64')
url="http://dnscrypt.org/"
license=('custom:ISC')
depends=('libsodium' 'systemd' 'libtool' 'ldns')
install=dnscrypt-proxy.install
backup=('etc/dnscrypt-proxy.conf')
source=("http://download.dnscrypt.org/$pkgname/$pkgname-$pkgver.tar.gz")
sha512sums=('fc1ae2194622eea51885117fbdbd3094c35beaf70e1c7e918f90af6ca5b73fe3b0cd69ddb91e659d50fefc6fafc9d0a774762b50fde886b5447fe54a874db66e')

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
