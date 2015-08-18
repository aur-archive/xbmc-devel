# Maintainer: Maxime Gauduin <alucryd@gmail.com>

pkgname=xbmc-devel
pkgver=13.1
pkgrel=2
pkgdesc='XBMC Media Center - Development snapshots'
arch=('i686' 'x86_64')
url='http://xbmc.org'
license=('GPL2')
depends=('bluez-libs' 'curl' 'ffmpeg' 'glew' 'hicolor-icon-theme' 'libcdio' 'libmad' 'libmicrohttpd' 'libmariadbclient' 'libmpeg2' 'libsamplerate' 'libssh' 'libxrandr' 'libxslt' 'lzo2' 'mesa' 'sdl_image' 'smbclient' 'taglib' 'tinyxml' 'xorg-xdpyinfo' 'yajl')
makedepends=('afpfs-ng' 'boost' 'cmake' 'doxygen' 'ftgl' 'git' 'gperf' 'jasper' 'java-environment' 'libcec' 'libnfs' 'libplist' 'nasm' 'shairplay' 'swig' 'unzip' 'zip')
optdepends=('afpfs-ng: Apple shares support'
            'libnfs: NFS shares support'
            'libplist: AirPlay support'
            'libcec: Pulse-Eight USB-CEC adapter support'
            'lirc: Remote controller support'
            'pulseaudio: PulseAudio support'
            'shairplay: AirPlay support'
            'udisks: Automount external drives'
            'unrar: Archived files support'
            'xorg-xinit: XBMC standalone')
install="${pkgname%-*}.install"
source=("xbmc-$pkgver.tar.gz::https://github.com/xbmc/xbmc/archive/13.1-Gotham.tar.gz"
        'enable-external-ffmpeg.patch'
        'xbmc.service'
        'polkit.rules')
sha256sums=('344b604eae2ddb47c032dd7964d01f27e6fcd7a8873c84c0841d5da75961a678'
            '0239e33e87292c7340ed2092f2b5f1e82f5e283b1f763fb125b3aee78f50c355'
            'f8b811ffe85c11b8da12895d65f0f8ccb2d4a3e196fd1bc5cd764cc96c4b6832'
            'c00027a32570fd3b651b26d2f63f4e7e7a162e877de6572bb6553ec007673029')

prepare() {
  cd xbmc-${pkgver}-Gotham

  patch -Np1 -i ../enable-external-ffmpeg.patch

  find -type f -name *.py -exec sed 's|^#!.*python$|#!/usr/bin/python2|' -i "{}" +
  sed 's|^#!.*python$|#!/usr/bin/python2|' -i tools/depends/native/rpl-native/rpl
  sed 's/python/python2/' -i tools/Linux/xbmc.sh.in
}

build() {
  cd xbmc-${pkgver}-Gotham

  export PYTHON_VERSION='2'

  ./bootstrap
  ./configure --prefix='/usr' --exec-prefix='/usr' --enable-{external-libraries,libbluray,pulse} --disable-{debug,non-free,static}
  make
}

package() {
  cd xbmc-${pkgver}-Gotham

  make DESTDIR="${pkgdir}" install
  install -m 755 tools/TexturePacker/TexturePacker "${pkgdir}"/usr/lib/xbmc/

  # Systemd
  install -dm 755 "${pkgdir}"/usr/lib/systemd/system
  install -m 644 ../xbmc.service "${pkgdir}"/usr/lib/systemd/system/

  # Polkit
  install -dm 700 "${pkgdir}"/etc/polkit-1/rules.d
  install -m 644 ../polkit.rules "${pkgdir}"/etc/polkit-1/rules.d/10-xbmc.rules

  # XBMC user home
  install -dm 700 "${pkgdir}"/var/lib/xbmc
  chown 420:420 "${pkgdir}"/var/lib/xbmc
}

# vim: ts=2 sw=2 et:
