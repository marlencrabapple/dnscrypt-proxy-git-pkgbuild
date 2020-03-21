# Maintainer: David Runge <dvzrv@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy
pkgver=2.0.40
pkgrel=1
pkgdesc="DNS proxy, supporting encrypted DNS protocols such as DNSCrypt v2 and DNS-over-HTTPS"
arch=('x86_64')
url="https://github.com/DNSCrypt/dnscrypt-proxy"
license=('custom:ISC')
depends=('glibc')
makedepends=('git' 'go-pie')
optdepends=('python-urllib3: for generate-domains-blacklist')
install="${pkgname}.install"
backup=("etc/${pkgname}/${pkgname}.toml"
        "etc/${pkgname}/blacklist.txt"
        "etc/${pkgname}/cloaking-rules.txt"
        "etc/${pkgname}/forwarding-rules.txt"
        "etc/${pkgname}/ip-blacklist.txt"
        "etc/${pkgname}/whitelist.txt"
)
source=("git+https://github.com/jedisct1/${pkgname}#tag=${pkgver}?signed"
        "${pkgname}.service"
        "${pkgname}.socket"
        "${pkgname}-configuration.patch::https://github.com/dvzrv/dnscrypt-proxy/commit/8d0fb58eaf5b2e315c9a243e34596104d4f2bff4.patch")
sha512sums=('SKIP'
            '9a93a2383f575cfc9c7ddbf42d075dd62877dbe50572cd853067834e0a8b66ff0173472d4b8465d357ab4cd33beedf4c39db03b8908a67180ffdb404a00a0c65'
            '56a56e87032da9316b392b0613124b0743673041596c717005541ae9b3994c7fc16c02497ea773d321f45d8e0f9ea8fda00783062cef4d5c8277b5b6f7cb10d5'
            '3144229a4b60a237f5f576650e6f7a34df90026307bb18b68b72bddc1cbdc14f4740c29ac570e1c337ff24439172b6f6e2f0d67ec5ccd38bea1572c7ad765ebb')
validpgpkeys=('54A2B8892CC3D6A597B92B6C210627AABA709FE1') # Frank Denis (Jedi/Sector One) <pgp@pureftpd.org

prepare() {
  mv -v "${pkgname}" "${pkgname}-${pkgver}"
  cd "$pkgname-$pkgver"
  patch -Np1 -i "../${pkgname}-configuration.patch"
  # create empty ip-blacklist.txt
  touch "${pkgname}/ip-blacklist.txt"
}

build() {
  cd "$pkgname-$pkgver/${pkgname}"
  go build -ldflags="-linkmode external -extldflags ${LDFLAGS} -s -w"
}

package() {
  cd "$pkgname-$pkgver"
  # executable
  install -vDm 755 "${pkgname}/${pkgname}" -t "${pkgdir}/usr/bin/"
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
  # utils
  install -vDm 644 utils/generate-domains-blacklists/*.{conf,txt} \
    -t "${pkgdir}/usr/share/${pkgname}/utils/generate-domains-blacklists"
  install -vDm 755 utils/generate-domains-blacklists/generate-domains-blacklist.py \
    "${pkgdir}/usr/bin/generate-domains-blacklist"
  # systemd service/socket
  install -vDm 644 "../${pkgname}."{service,socket} \
    -t "${pkgdir}/usr/lib/systemd/system/"
  # license
  install -vDm 644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}"
  # docs
  install -vDm 644 {ChangeLog,README.md} \
    -t "${pkgdir}/usr/share/doc/${pkgname}"
}
# vim:set ts=2 sw=2 et:
