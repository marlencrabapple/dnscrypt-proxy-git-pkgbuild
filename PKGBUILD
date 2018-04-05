# Maintainer: David Runge <dave@sleepmap.de>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=2.0.8
pkgrel=3
pkgdesc="A flexible DNS proxy, with support for modern encrypted DNS protocols such as DNSCrypt v2 and DNS-over-HTTP/2."
arch=('x86_64')
url="https://dnscrypt.info"
license=('custom:ISC')
depends=('glibc')
makedepends=('git' 'go')
install="${pkgname}.install"
backup=("etc/${pkgname}/${pkgname}.toml"
        "etc/${pkgname}/blacklist.txt"
        "etc/${pkgname}/cloaking-rules.txt"
        "etc/${pkgname}/forwarding-rules.txt"
        "etc/${pkgname}/ip-blacklist.txt"
)
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/jedisct1/${pkgname}/archive/${pkgver}.tar.gz"
        'configuration.diff')
sha512sums=('c7e7d5d72fa6874b2b6b4deaaf6c80e4a2e812670b71bf7a308535a5773e84e249263bbb66d18fb844d8e440703facb0902f3872b117433582696695cb5a7265'
            '2206b71aa05d81c962f6a93d837731946aacbcc36ee19320a9cdf379c105d04f97044be702ac83e96492ece358148227ef04ed45d17e54dd4b84f5b4d66575bf')

prepare() {
  cd "$pkgname-$pkgver"
  patch -Np1 -i ../configuration.diff
  # create empty ip-blacklist.txt
  touch "${pkgname}/ip-blacklist.txt"
  # set GOPATH
  export GOPATH=`pwd`
  # symlink upstream's vendor to src
  ln -sfv vendor src
}

build() {
  cd "$pkgname-$pkgver/${pkgname}"
  go build -ldflags="-s -w"
}

package() {
  cd $pkgname-$pkgver
  # executable
  install -vDm 755 "${pkgname}/${pkgname}" "${pkgdir}/usr/bin/${pkgname}"
  # configuration
  install -vDm 644 "${pkgname}/example-${pkgname}.toml" \
    "${pkgdir}/etc/${pkgname}/${pkgname}.toml"
  install -vDm 644 "${pkgname}/example-blacklist.txt" \
    "${pkgdir}/etc/${pkgname}/blacklist.txt"
  install -vDm 644 "${pkgname}/example-cloaking-rules.txt" \
    "${pkgdir}/etc/${pkgname}/cloaking-rules.txt"
  install -vDm 644 "${pkgname}/example-forwarding-rules.txt" \
    "${pkgdir}/etc/${pkgname}/forwarding-rules.txt"
  install -vDm 644 "${pkgname}/ip-blacklist.txt" \
    "${pkgdir}/etc/${pkgname}/ip-blacklist.txt"
  # systemd service/socket
  install -vDm 644 "systemd/${pkgname}."{service,socket} \
    -t "$pkgdir/usr/lib/systemd/system/"
  # license
  install -vDm 644 LICENSE "$pkgdir/usr/share/licenses/${pkgname}/LICENSE"
  # docs
  install -vDm 644 {ChangeLog,README.md} \
    -t "$pkgdir/usr/share/doc/${pkgname}"
}
# vim:set ts=2 sw=2 et:
