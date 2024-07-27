# Maintainer: David Runge <dvzrv@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

pkgname=dnscrypt-proxy-git
_pkgname=dnscrypt-proxy
pkgver=2.1.5
_commit=8744c6f10da322921d1c12e24105bcfe813e42a6  # refs/tags/2.1.5
pkgrel=4
pkgdesc="A flexible DNS proxy, with support for encrypted DNS protocols"
arch=(x86_64)
url="https://github.com/DNSCrypt/dnscrypt-proxy"
license=(ISC)
depends=(glibc)
makedepends=(git go)
optdepends=('python-urllib3: for generate-domains-blocklist')
provides=('dnscrypt-proxy')
replaces=('dnscrypt-proxy')
conflicts=('dnscrypt-proxy')
backup=(
  etc/$_pkgname/$_pkgname.toml
  etc/$_pkgname/allowed-ips.txt
  etc/$_pkgname/allowed-names.txt
  etc/$_pkgname/blocked-ips.txt
  etc/$_pkgname/blocked-names.txt
  etc/$_pkgname/cloaking-rules.txt
  etc/$_pkgname/forwarding-rules.txt
)
# NOTE: LTO breaks reproducibility :(
#options=(!lto)
source=(
  git+$url.git
  $_pkgname.service
  $_pkgname.socket
  $_pkgname-$pkgver-configuration.patch::https://github.com/dvzrv/dnscrypt-proxy/commit/66ffcf524b55b4f06cfaf4e37c8b8c6dcc10d0df.patch
)
sha512sums=('SKIP'
            '50e6c878115c96e72f6118008e92871957a699d89bd0b85c80af45e6880a30b0832995e4718ab585b086049cc64e2b0759f8f4263ef814d74929933534403f92'
            '9f9deeb55b32ea7c289fc17e12c353dcb955b695259311ab20a0ff6c22cbd7a22ef4de1e06a4260b8923721f98754520316669191e69869dce3b69d1329e5c36'
            'fa45be971c1bb401527ba8f5043312f021fad0d417bd764ab6550637d5488a00d886d966df21be48d4503f1b5e326b875bac42ee18613827326c12af5ce19359')
b2sums=('SKIP'
        '2f882cf27591e180cdda4f5267f13356eaff965f4c18c3bd36bf68582a3e8605660b6f292cd20a337fda49e8608719869df3a579150c811f8a402c6afb585afd'
        '5d057d0512bbfdb97b9033fd93a60259764b3b0dde5f8e1320cedd67b7cf9cb524a8b8df8142ca6071fcd5c17cd12467a79712219851e772d893f33109277623'
        'ef48e0e6428e75c6f17c8e16d3eca12d3edd2175d254f42acfb7a915a0d3639071afc37acd7692b0bb7bcc9eb60a4acee6edb67302be37bd8a7b2351f26e3151')
validpgpkeys=('54A2B8892CC3D6A597B92B6C210627AABA709FE1') # Frank Denis (Jedi/Sector One) <pgp@pureftpd.org

pkgver() {
  cd "$_pkgname"
  git describe --long --abbrev=7 | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  cd $_pkgname
  patch -Np1 -i ../$_pkgname-$pkgver-configuration.patch
}

build() {
  cd $_pkgname/$_pkgname
  export CGO_CPPFLAGS="$CPPFLAGS"
  export CGO_CFLAGS="$CFLAGS"
  export CGO_CXXFLAGS="$CXXFLAGS"
  export CGO_LDFLAGS="$LDFLAGS"
  export GOPATH="$srcdir"
  export GOFLAGS="-buildmode=pie -mod=readonly -modcacherw"

  go build -ldflags "-compressdwarf=false -linkmode external" .
}

check() {
  cd $_pkgname
  go test ./...
}

package() {
  local _config

  cd $_pkgname
  # executable
  install -vDm 755 $_pkgname/$_pkgname -t "$pkgdir/usr/bin/"
  # config files
  install -vDm 644 $_pkgname/example-$_pkgname.toml "$pkgdir/etc/$_pkgname/$_pkgname.toml"
  for _config in {{allowed,blocked}-{ips,names},{cloaking,forwarding}-rules,captive-portals}.txt; do
    install -vDm 644 $_pkgname/example-$_config "$pkgdir/etc/$_pkgname/$_config"
  done
  # utils
  install -vDm 644 utils/generate-domains-blocklist/*.{conf,txt} -t "$pkgdir/usr/share/$_pkgname/utils/generate-domains-blocklist"
  install -vDm 755 utils/generate-domains-blocklist/generate-domains-blocklist.py "$pkgdir/usr/bin/generate-domains-blocklist"
  # systemd service/socket
  install -vDm 644 ../$_pkgname.{service,socket} -t "$pkgdir/usr/lib/systemd/system/"
  # license
  install -vDm 644 LICENSE -t "$pkgdir/usr/share/licenses/$_pkgname"
  # docs
  install -vDm 644 {ChangeLog,README.md} -t "$pkgdir/usr/share/doc/$_pkgname"
}
# vim:set ts=2 sw=2 et:
