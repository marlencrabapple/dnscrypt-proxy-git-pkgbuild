# Maintainer: David Runge <dave@sleepmap.de>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=2.0.11
pkgrel=1
pkgdesc="A flexible DNS proxy, with support for modern encrypted DNS protocols such as DNSCrypt v2 and DNS-over-HTTP."
arch=('x86_64')
url="https://dnscrypt.info"
license=('custom:ISC')
depends=('glibc')
makedepends=('git' 'go-pie')
install="${pkgname}.install"
backup=("etc/${pkgname}/${pkgname}.toml"
        "etc/${pkgname}/blacklist.txt"
        "etc/${pkgname}/cloaking-rules.txt"
        "etc/${pkgname}/forwarding-rules.txt"
        "etc/${pkgname}/ip-blacklist.txt"
        "etc/${pkgname}/whitelist.txt"
)
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/jedisct1/${pkgname}/archive/${pkgver}.tar.gz"
        'configuration.diff')
sha512sums=('5e306c3bff65d0375b650666e2191c6f54e72bb0d2d2f6f8f7b941ffc063eb7eb244a52eb69b0b008dc566e9a6ce8a8f75929edb6762fad4751d966aa2da98a0'
            '0c6ab334eea4f295d6b86358cad9689d342c63a2cf428007df38fb1d0a72be99da79ce3775396ed410982622dce7e322326558c02d198487611ca6fa450f63f6')

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
  install -vDm 644 "${pkgname}/example-whitelist.txt" \
    "${pkgdir}/etc/${pkgname}/whitelist.txt"
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
