# Maintainer: Ian P Bradley <crabapp@hikki.tech>
# Contributor: John Fawkes
# Contributor: David Runge <dvzrv@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Techlive Zheng <techlivezheng at gmail dot com>
# Contributor: peace4all <markspost at rocketmail dot com>

_pkgname=dnscrypt-proxy
pkgname=$_pkgname-git
pkgver=2.1.12.r28.g4511c18
pkgrel=1
pkgdesc="A flexible DNS proxy, with support for encrypted DNS protocols"
arch=(any)
url="https://github.com/DNSCrypt/dnscrypt-proxy"
license=(ISC)
depends=(glibc openssl)
makedepends=(git go openssl perl)
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
#options=(!lto)
_commit=4511c182fee62edd9ed17ac9906ec1d458e009d3
source=(
  git+$url.git#commit=$_commit
  $_pkgname.service
  $_pkgname.socket
  001-set-defaults-$_pkgname.toml.patch
)
sha512sums=('ae472857c85905bb299f37be7fa391c9acc7b30a39f1598bf1562f1ee441789ab51c16ab3f10477238d834ddd2dc9c83f74e0f4eb3527184d7490c72952cacbb'
            '50e6c878115c96e72f6118008e92871957a699d89bd0b85c80af45e6880a30b0832995e4718ab585b086049cc64e2b0759f8f4263ef814d74929933534403f92'
            '9f9deeb55b32ea7c289fc17e12c353dcb955b695259311ab20a0ff6c22cbd7a22ef4de1e06a4260b8923721f98754520316669191e69869dce3b69d1329e5c36'
            'ef11bd7950b1ee49e772cf708c8c9741bbc9730ba05c9ce5544141dbbd079911766235f0cd3f437be6073986f2971dbdde00d49ef70360c0723dbb9e7337a7a6')
b2sums=('1d069ecd5b2c017832feb9709725df2e8f2bcdef14b1409d98319f80e491e9b01ebea2943be8be94893f187aa3694b70e8ccded2a86796ed1daf2ef2e939bfec'
        '2f882cf27591e180cdda4f5267f13356eaff965f4c18c3bd36bf68582a3e8605660b6f292cd20a337fda49e8608719869df3a579150c811f8a402c6afb585afd'
        '5d057d0512bbfdb97b9033fd93a60259764b3b0dde5f8e1320cedd67b7cf9cb524a8b8df8142ca6071fcd5c17cd12467a79712219851e772d893f33109277623'
        '8989aa2256b753189aa6a14e902906c024427bb9b60614124b566192bd6776f6975f620195b37022a629b882651d4520d4d4b7c9b1788e38139dcc69e165d020')
validpgpkeys=('54A2B8892CC3D6A597B92B6C210627AABA709FE1') # Frank Denis (Jedi/Sector One) <pgp@pureftpd.org>

pkgver() {
  cd "$_pkgname"
  git describe --long --abbrev=7 | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  cd "$_pkgname"
  patch -Np1 -i ../001-set-defaults-$_pkgname.toml.patch
  go mod tidy
}

build() {
  cd "$_pkgname/$_pkgname"
  
  export CGO_CPPFLAGS="$CPPFLAGS"
  export CGO_CFLAGS="$CFLAGS"
  export CGO_CXXFLAGS="$CXXFLAGS"
  export CGO_LDFLAGS="$LDFLAGS"
  export GOPATH="$srcdir"
  export GOFLAGS="-buildmode=pie -mod=readonly -modcacherw"
  local _target_arch_args=(-cover)

  CGO_ENABLED="$(perl -e 'use v5.40; say $ENV{GOHOSTARCH} && ($ENV{GOHOSTARCH} eq $ENV{CARCH} =~ s/_?v[0-9\.]{1,3}$//r) ? 1 : 0')"
  
  if [[ -z "${CARCH%%x86_64_v[2-4]}" ]]; then
    export GOAMD64="${CARCH#x86_64_}"
  elif [[ -z "${CARCH%%aarch64v*}" ]]; then
    export GOARM64="${CARCH#aarch64}"
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
