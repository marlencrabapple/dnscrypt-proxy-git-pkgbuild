# Maintainer: David Runge <dave@sleepmap.de>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=2.0.16
pkgrel=1
pkgdesc="DNS proxy, supporting encrypted DNS protocols such as DNSCrypt v2 and DNS-over-HTTP."
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
        "${pkgname}.service"
        "${pkgname}.socket"
        'configuration.diff')
sha512sums=('f138df20560dd440a2ed390c1468d630191ae7b0e50521b4dde3fa7ef4377c3ae6409e8c547858bace53216c84aeeea6794305546b9ff87832f704c160c6782f'
            '3b24392c1ba20a38863f2424c9d891aef84c48239340a124ee569e564f04dd06d356e03d95ef0a723c2c43a1c03c8efb3d029c99810f93ecee968e3eefbc51ea'
            '17175397a5a35692f300d6caff84eb236b21a6e41a870bca966c5576f0db2bc7556d6a214d2f7e985fe9e0be99ef6e0bb067f29cebd41c2ea374540d6f4bd990'
            '6144f3d33f3d85c9a4e5573f88e92f1b9d7118fd654072eeac6c3f76085086d4b2464e1d3579d8501153f453bc5125859d148fc3b3486d26368d1f51911aeb33')

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
  install -vDm 644 "../${pkgname}."{service,socket} \
    -t "${pkgdir}/usr/lib/systemd/system/"
  # license
  install -vDm 644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  # docs
  install -vDm 644 {ChangeLog,README.md} \
    -t "${pkgdir}/usr/share/doc/${pkgname}"
}
# vim:set ts=2 sw=2 et:
