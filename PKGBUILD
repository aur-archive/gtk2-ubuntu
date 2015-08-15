# Maintainer: Xiao-Long Chen <chenxiaolong@cxl.epac.to>
# Contributor: hbdee <hbdee.arch@gmail.com>
# Contributor: Abi hafshin <abi@hafs.in>

pkgname=gtk2-ubuntu
_ubuntu_rel=0ubuntu1
pkgver=2.24.24
pkgrel=1
pkgdesc="GTK+ is a multi-platform toolkit (v2)"
arch=('i686' 'x86_64')
url="http://www.gtk.org/"
license=('LGPL')
depends=('atk' 'pango' 'libxcursor' 'libxinerama' 'libxrandr' 'libxi' 'libxcomposite' 'libxdamage' 'shared-mime-info' 'libcups' 'gtk-update-icon-cache' 'python2')
makedepends=('pkg-config' 'gobject-introspection' 'gtk-doc')
provides=("gtk2=${pkgver}")
conflicts=('gtk2')
replaces=('gtk2-docs')
backup=('etc/gtk-2.0/gtkrc')
options=('!libtool')
install=arch_gtk2.install
source=("http://ftp.gnome.org/pub/gnome/sources/gtk+/2.24/gtk+-${pkgver}.tar.xz"
        "https://launchpad.net/ubuntu/+archive/primary/+files/gtk+2.0_${pkgver}-${_ubuntu_rel}.debian.tar.xz"
        'arch_xid-collision-debug.patch'
        'fix-ubuntumenuproxy-build.patch')
sha512sums=('75dbeb9aa3546e6fd2861e1d352fa347f6163b25f8292680d53fe13c73c310d90b447fab1ba3bada9e878a1f7c4cef3e279460a698e0282bd13b495606614765'
            '100b6b85447e561d37fa9048836f1af01291b1942986b46a50716b3e91973a98a913892908b0a5cdabafc656f1f381df103266ba64dbdd735dad668b34fc7eaa'
            '89e3223c86731a76b12d39245f1ec4cf8b4860ab1d11d74a10e5deb0db302f20b4bb5d9034da1d8aeb96369dbdeddcdd2f28943270dc501476c694562b960159'
            '4b27afecb82e3517c2584665f8d2a2911193254a00b82ff8064caf4016ab029c76e99af502bb9271a66209594b50b2729797defcc9512d341aa61aff4f64ec24')
prepare() {
  cd "${srcdir}/gtk+-${pkgver}"

  patch -p1 -i "${srcdir}/arch_xid-collision-debug.patch"
  
  # Apply Ubuntu patches

  # Certain patches should not be used
    # Multiarch
      sed -i '/041_ia32-libs.patch/d' "${srcdir}/debian/patches/series"
      sed -i '/098_multiarch_module_path.patch/d' "${srcdir}/debian/patches/series"
    # Static linking stuff for udebs
      sed -i '/001_static-linking-dont-query-immodules.patch/d' "${srcdir}/debian/patches/series"
      sed -i '/002_static-linking-dont-build-perf.patch/d' "${srcdir}/debian/patches/series"
    # Breaks pkgconfig
      sed -i '/003_gdk.pc_privates.patch/d' "${srcdir}/debian/patches/series"
    # gtk.immodules is in /etc/gtk-2.0/ in Arch  Linux, not
    # /usr/lib/gtk-2.0/2.10.0/
      sed -i '/011_immodule-cache-dir.patch/d' "${srcdir}/debian/patches/series"

  # Build fix from György Balló
  patch -p1 -i "${srcdir}/fix-ubuntumenuproxy-build.patch"

  for i in $(cat "${srcdir}/debian/patches/series" | grep -v '#'); do
    patch -p1 -i "${srcdir}/debian/patches/${i}"
  done
}

build() {
  cd "${srcdir}/gtk+-${pkgver}"

  autoreconf -vfi

  CXX=/bin/false ./configure \
    --prefix=/usr \
    --sysconfdir=/etc \
    --localstatedir=/var \
    --with-xinput=yes \
    --enable-test-print-backend

  # https://bugzilla.gnome.org/show_bug.cgi?id=655517
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool

  make
}

package() {
  cd "${srcdir}/gtk+-${pkgver}"

  make DESTDIR="${pkgdir}" install
  sed -i "s#env python#env python2#" "${pkgdir}/usr/bin/gtk-builder-convert"
  echo 'gtk-fallback-icon-theme = "gnome"' > "${pkgdir}/etc/gtk-2.0/gtkrc"
  # Use the official gtk-update-icon-cache package
  rm -v "${pkgdir}/usr/bin/gtk-update-icon-cache"
}

# vim:set ts=2 sw=2 et:
