# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=1.9.4
pkgrel=1
pkgdesc="A tool for securing communications between a client and a DNS resolver"
arch=('i686' 'x86_64')
url="http://dnscrypt.org/"
license=('custom:ISC')
depends=('libsodium' 'systemd' 'libtool' 'ldns')
install=dnscrypt-proxy.install
backup=('etc/dnscrypt-proxy.conf')
source=("http://download.dnscrypt.org/$pkgname/$pkgname-$pkgver.tar.gz")
sha512sums=('b19103dc39655f199c85ec69daab949d07adc12e64411d2323308afb398e6ac6563bbc17f2e317bfe1e6c95c810487d8d62da3f4c7f7257bdcf837cd0390bfb0')

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
