# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=1.6.1
pkgrel=1
pkgdesc="A tool for securing communications between a client and a DNS resolver"
arch=('i686' 'x86_64')
url="http://dnscrypt.org/"
license=('custom:ISC')
depends=('libsodium' 'systemd')
install=dnscrypt-proxy.install
source=("http://download.dnscrypt.org/$pkgname/$pkgname-$pkgver.tar.gz"
        'Remove-network.target-dependency-from-dnscrypt-proxy.patch')
sha512sums=('8d49be73e4b2fe1131349846191f54c7f212ff301a6a6c2a77505225f81b0615e968c945013e732c4fce3b6090606245ec7343fb0d6145bb10b67dd4112d2367'
            'a3fd405cc635ec3b8564fbfc769504eeaf77b693fa521df44be7beb1d5abea971b81905a11357870a6fb5ba8d129c91bf9cc337cb730b733c3d3c047854a4021')

prepare() {
  cd $pkgname-$pkgver

  sed -i 's|/usr/local/sbin|/usr/bin|' dnscrypt-proxy.service
}

build() {
  cd $pkgname-$pkgver

  ./configure --prefix=/usr --sbindir=/usr/bin --with-systemd
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
