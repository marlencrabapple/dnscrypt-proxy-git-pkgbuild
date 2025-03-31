# Maintainer: Ian P Bradley <crabapp@hikki.tech>
# Contributor: John Fawkes
# Contributor: David Runge <dvzrv@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

_pkgname=dnscrypt-proxy
pkgname=$_pkgname-git
pkgver=2.1.8.r3.gae2d7d2
pkgrel=2
pkgdesc="A flexible DNS proxy, with support for encrypted DNS protocols"
arch=(any)
url="https://github.com/DNSCrypt/dnscrypt-proxy"
license=(ISC)
depends=(glibc openssl)
makedepends=(git go openssl)
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
install=$_pkgname.install
# NOTE: LTO breaks reproducibility :(
options=(!lto)
source=(
  git+$url.git
  $_pkgname.service
  $_pkgname.socket
  001-set-defaults-$_pkgname.toml.patch
)
sha512sums=('SKIP'
            '50e6c878115c96e72f6118008e92871957a699d89bd0b85c80af45e6880a30b0832995e4718ab585b086049cc64e2b0759f8f4263ef814d74929933534403f92'
            '9f9deeb55b32ea7c289fc17e12c353dcb955b695259311ab20a0ff6c22cbd7a22ef4de1e06a4260b8923721f98754520316669191e69869dce3b69d1329e5c36'
            'ca206404be9c357f11ebfa25388315e4452ff0ff0d6b8515abe2747c5d5e6020f7627bdf297e819afcdb1930616cb5c6b167b65a5641380c6ac4b8e09b1bbba4')
b2sums=('SKIP'
        '2f882cf27591e180cdda4f5267f13356eaff965f4c18c3bd36bf68582a3e8605660b6f292cd20a337fda49e8608719869df3a579150c811f8a402c6afb585afd'
        '5d057d0512bbfdb97b9033fd93a60259764b3b0dde5f8e1320cedd67b7cf9cb524a8b8df8142ca6071fcd5c17cd12467a79712219851e772d893f33109277623'
        'e6d88990dadaf9851247e30ef9998217a10bbc5e20a84ee4b66fe46e9d5e0f0862a3ed5193466585d7a8c4af15ad3eb0ea9c63b62c83cc24e6a6953c14727498')
validpgpkeys=('54A2B8892CC3D6A597B92B6C210627AABA709FE1') # Frank Denis (Jedi/Sector One) <pgp@pureftpd.org>

pkgver() {
  cd "$_pkgname"
  git describe --long --abbrev=7 | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  cd "$_pkgname"
  patch -Np1 -i ../001-set-defaults-$_pkgname.toml.patch
}

build() {
  cd "$_pkgname/$_pkgname"
  
  export CGO_CPPFLAGS="$CPPFLAGS"
  export CGO_CFLAGS="$CFLAGS"
  export CGO_CXXFLAGS="$CXXFLAGS"
  export CGO_LDFLAGS="$LDFLAGS"
  export GOPATH="$srcdir"
  export GOFLAGS="-buildmode=pie -mod=readonly -modcacherw"
  
  if [[ -z "${CARCH%%x86_64_v[2-4]}" ]]; then
    export GOAMD64="${CARCH#x86_64_}"
  fi

  go build -ldflags "-compressdwarf=false -linkmode external" .
}

check() {
  cd "$_pkgname"
  go test ./...
}

package() {
  set -x
  cd "$_pkgname"
  
  # executable
  install -vDm 755 $_pkgname/$_pkgname -t "$pkgdir/usr/bin/"
  
  # config files
  install -vDm 644 $_pkgname/example-$_pkgname.toml "$pkgdir/etc/$_pkgname/$_pkgname.toml"

  local _configfiles=($(ls {{allowed,blocked}-{ips,names},{cloaking,forwarding}-rules,captive-portals}.txt))

  local _config
  for _config in "${configfiles[@]}"; do
    install -vDm 644 "$_pkgname/example-$_config" \
     "$pkgdir/etc/$_pkgname/$_config"
  done

  # utils
  install -vDm 644 utils/generate-domains-blocklist/*.{conf,txt} \
   -t "$pkgdir/usr/share/$_pkgname/utils/generate-domains-blocklist"

  install -vDm 755 utils/generate-domains-blocklist/generate-domains-blocklist.py \
   "$pkgdir/usr/bin/generate-domains-blocklist"
  
  # systemd service/socket
  install -vDm 644 ../$_pkgname.{service,socket} \
   -t "$pkgdir/usr/lib/systemd/system/"
  
  # license
  install -vDm 644 LICENSE -t "$pkgdir/usr/share/licenses/$_pkgname"
  
  # docs
  install -vDm 644 {ChangeLog,README.md} -t "$pkgdir/usr/share/doc/$_pkgname"
}
# vim:set ts=2 sw=2 et:
