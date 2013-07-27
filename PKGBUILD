# Maintainer: Felix Yan <felixonmars@gmail.com>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=1.3.2
pkgrel=1
pkgdesc="A tool for securing communications between a client and a DNS resolver"
arch=('i686' 'x86_64')
url="http://dnscrypt.org/"
license=('custom:ISC')
depends=(libsodium)
source=(http://download.dnscrypt.org/$pkgname/$pkgname-$pkgver.tar.bz2 
	conf.d.file 
	dnscrypt-proxy.service)
backup=(etc/conf.d/dnscrypt-proxy)

build() {
  cd "$srcdir/$pkgname-$pkgver"
  CFLAGS="$CFLAGS -fPIC" ./configure --prefix=/usr --sbindir=/usr/bin
  make -j2
}

package() {
  cd "$srcdir/$pkgname-$pkgver"
  make DESTDIR="$pkgdir" install

  mkdir -p "$pkgdir"/{usr/share/{licenses,doc}/$pkgname,etc/conf.d,usr/lib/systemd/system}
  install -m 644 COPYING "$pkgdir"/usr/share/licenses/$pkgname
  install -m 644 AUTHORS NEWS README README.markdown "$pkgdir"/usr/share/doc/$pkgname
  install -m 644 "$srcdir"/conf.d.file "$pkgdir"/etc/conf.d/$pkgname
  install -m 644 "$srcdir"/dnscrypt-proxy.service "$pkgdir"/usr/lib/systemd/system
  rm -rf "$pkgdir"/usr/{lib/*.{l,}a,include}
}

sha512sums=('969219c5655ca7bcb8cbfb79d9dae9b27a79489bae7451e6e478ab5cee1bc22f0dc1758e9ff51ac2e80ec4230e6fe4eaba3a5ff98c3ddbde8d9dc24a51050bab'
            '2271eab1baf1a7192a8daaa1b271c24a3a3fa498432ac99c2e4fb3c939590f09aab582427ae874fce7969ece365ccb7a2e946566e5a0c650d93c34d23b536b61'
            '33e8f95c38a10a9e05aadeb369616f6dfaa8b32bc28b9f8ab9ae29dfc3b03514442e7172b1f52c56665317b61cad7d130a0e1dc00d6aceaa59eb465e63d079bc')
