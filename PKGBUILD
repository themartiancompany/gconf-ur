# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Truocolo <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
# Maintainer: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Maintainer: Pellegrino Prevete (dvorak) <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Maintainer: Pierce Thompson <pierce@insprill.net>
# Contributor: João Figueiredo <islandc0der@chaotic.cx>
# Contributor: Jan de Groot <jan@archlinux.org>

_proj="gnome"
pkgname=gconf
pkgver=3.2.6+11+g07808097
pkgrel=13
pkgdesc="An obsolete configuration database system"
_http="https://gitlab.${_proj}.org"
_ns="Archive"
url="${_http}/${_ns}/gconf"
arch=(
  $CARCH
)
license=(
  'LGPL-2.0-only'
)
depends=(
  'libxml2'
  'polkit'
  'libldap'
  'dbus-glib'
)
makedepends=(
  'git'
  'intltool'
  'gtk-doc'
  'gobject-introspection'
  'gnome-common'
  'glib2-devel'
)
install=gconf.install
# The latest and last commit, dug out from deep within the waves of time...
_commit="0780809731c8ab1c364202b1900d3df106b28626"
source=(
  "git+https://gitlab.gnome.org/Archive/gconf.git#commit=$_commit"
  "01_xml-gettext-domain.patch gconf-reload.patch"
  "gconf-merge-schema"
  "gconfpkg"
  "gconf-"{"install","remove"}".hook"
)
sha256sums=(
  'SKIP'
  'c883dec2b96978874a53700cfe7f26f24f8296767203e970bc6402b4b9945eb8'
  '567b78d8b4b4bbcb77c5f134d57bc503c34867fcc6341c0b01716bcaa4a21694'
  'ee6b6e6f4975dad13a8c45f1c1f0547a99373bdecdcd6604bfc12965c328a028'
  'bf1928718caa5df2b9e54a13cfd0f15a8fe0e09e86b84385ce023616a114e898'
  '2732b2a6b187c5620105a036bde12edee99669605f70cbde56fe5f39619c3dc0'
  '436a65ff290095bc3d35d7d6297cf4d647f61e9f9922cea7ef9f1e251b447ff7'
)

prepare() {
  cd \
    "${pkgname}"
  # Patch from fedora - reloads gconf after installing schemas
  patch \
    -Np1 \
    -i \
    "../gconf-reload.patch"
  # http://bugzilla.gnome.org/show_bug.cgi?id=568845
  patch \
    -Np1 \
    -i \
    "../01_xml-gettext-domain.patch"
  NOCONFIGURE=1 \
  ./autogen.sh
}

build() {
  local \
    _configure_opts=()
  _configure_opts=(
    --prefix="/usr" --sysconfdir="/etc"
    --localstatedir="/var"
    --libexecdir="/usr/lib"
    --enable-defaults-service
    --disable-gtk-doc
    --disable-static
    --disable-orbit
    --disable-gsettings-backend
  )
  cd \
    "${pkgname}"
  ./configure \
    "${_configure_opts[@]}"
  sed \
    -i \
    -e \
      's/ -shared / -Wl,-O1,--as-needed\0/g' \
    "libtool"
  make
}

check() {
  cd \
    "${pkgname}"
  make \
    check
}

package() {
  DESTDIR="${pkgdir}" \
  make \
    -C "${pkgname}" \
    install
  install \
    -d \
      "${pkgdir}/etc/gconf/gconf.xml.system"
  install \
    -D \
    "gconf-merge-schema" \
    "gconfpkg" \
    -t \
    "${pkgdir}/usr/bin"
  install \
    -Dm644 \
    "./"*".hook" \
    -t \
    "${pkgdir}/usr/share/libalpm/hooks"
  # fix dbus policy location - --with-dbusdir doesn't work
  install \
    -Dm644 \
    "${pkgdir}/etc/dbus-1/system.d/org.gnome.GConf.Defaults.conf" \
    -t \
    "${pkgdir}/usr/share/dbus-1/system.d"
  rm \
    -rf \
    "${pkgdir}/etc/dbus-1/"
}
