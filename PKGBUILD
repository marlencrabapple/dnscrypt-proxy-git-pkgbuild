# Maintainer: David Runge <dave@sleepmap.de>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=2.0.9
pkgrel=1
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
        "etc/${pkgname}/whitelist.txt"
)
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/jedisct1/${pkgname}/archive/${pkgver}.tar.gz"
        'configuration.diff')
sha512sums=('4a7273e0a779b4103998930d5e07cd49678b47ec1de242e7865e3f8519ba9d15ce4e672c240e46eff97c407e97ea47f05508c5e70c03dfa569216d403625659e'
            'e722e01e8e35778bcabe419847d07597415b10c486d4e4614fbbaf71d03be2243c007047370db9def10f39842bc71ffe71a227eaee1fb5d90b73341ced6fd579')

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
