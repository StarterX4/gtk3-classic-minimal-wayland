# Maintainer: Luke Horwell <code@horwell.me>
# Contributor: Jonathon Fernyhough <jonathon+m2x.dev>
# Contributor: Tomasz Gąsior <tomaszgasior.pl>

# This file is based on original PKGBUILD of gtk4 package.
# https://git.archlinux.org/svntogit/packages.git/plain/trunk/PKGBUILD?h=packages/gtk4

__arch_pkg_commit="ef36b28a894a3de835464d89a3ac0bc2898c2317"
_gtkver=4.7.1

pkgbase=gtk4-minimal
pkgname=($pkgbase gtk-update-icon-cache)
pkgver=${_gtkver}
pkgrel=100
pkgdesc="gtk4 patched to provide a more classic experience (minimal and Wayland-only)"
url="https://github.com/StarterX4/gtk4-classic-minimal-wayland"
conflicts=(gtk4)
provides=(gtk4=$_gtkver
          libgtk-4.so)
options=(!ccache)
arch=(x86_64)
license=(LGPL)
makedepends=(
	git gobject-introspection graphene sassc meson quilt vulkan-headers

	atk cairo libxcursor libxinerama libxrandr libxi libepoxy gdk-pixbuf2 fribidi
	libxcomposite libxdamage pango libcloudproviders shared-mime-info shaderc wayland libxkbcommon
	json-glib librsvg wayland-protocols desktop-file-utils mesa

)
install=gtk4.install
source=(
	# Patch files.
	series
	csd__disabled-by-default.patch
	file-chooser__places-sidebar.patch
	other__remove_dead_keys_underline.patch

	# Theme CSS stylesheet.
	smaller-adwaita.css

	# GTK source code.
	"https://download.gnome.org/sources/gtk/${pkgver%.*}/gtk-$_gtkver.tar.xz"

	# Arch Linux package files.
	settings.ini
	"gtk-query-immodules-4.0.hook::https://raw.githubusercontent.com/archlinux/svntogit-packages/packages/gtk4/trunk/gtk4-querymodules.hook"
	gtk-update-icon-cache.{hook,script}
)
sha256sums=('7a60ebe4c6ef84f31334dff0a1bd48876d7dbd7d899a194436aef748e0cf5326'
            'caa4da5e786a38e788617d6c9a844dfc604038d2a5d57033273859cad46d14cd'
            '5a422025358243567111806062b4be117706afe276e0c842bb238ae208a0d5d0'
            'b92a82568a0f5c1c897561efafb55deb2331450d53377ab230def71012d8ccfc'
            'ba93f62e249f2713dbfe6c82de1be4ac655264d6407ed3dc5e05323027520f31'
            'SKIP'
            'SKIP'
            'SKIP'
            '2d435e3bec8b79b533f00f6d04decb1d7c299c6e89b5b175f20be0459f003fe8'
            'f1d3a0dbfd82f7339301abecdbe5f024337919b48bd0e09296bb0e79863b2541')

prepare()
{
	cd gtk-$_gtkver
	QUILT_PATCHES=.. quilt push -av

	rm -f "$srcdir"/gtk-"$_gtkver"/gtk/theme/Default/Default-{hc,hc-dark,dark,light}.css
	cat "$srcdir/smaller-adwaita.css" | tee -a "$srcdir"/gtk-"$_gtkver"/gtk/theme/Default/Default-{hc,hc-dark,dark,light}.css > /dev/null
}

build()
{
	CFLAGS+=" -O3 -pipe -fno-plt -DG_DISABLE_CAST_CHECKS"
	CXXFLAGS+=" -O3 -pipe -fno-plt"
	CC="ccache gcc"
	PATH="/usr/lib/ccache/bin/:$PATH"
	alias cc="ccache gcc"
	export CACHE_DIR="/home/runner/work/gtk3-classic-minimal-wayland/gtk3-classic-minimal-wayland/.ccache"
	# 64-bit
	arch-meson --buildtype release --strip gtk-4.7.1 build \
		-D broadway-backend=false \
        -D demos=false \
        -D build-tests=false \
        -D build-examples=false \
        -D win32-backend=false \
        -D macos-backend=false  \
        -D colord=disabled \
        -D cloudproviders=disabled \
        -D gtk_doc=false \
        -D f16c=disabled \
        -D x11-backend=false \
        -D tracker=disabled \
        -D man-pages=false \
		-D vulkan=enabled \
		-D print-cups=disabled \
		-D media-ffmpeg=disabled \
		-D media-gstreamer=disabled \
		-D wayland-backend=true
	ninja -C build
}

package_gtk4-minimal()
{
	depends=(
		glib2 cairo pango fribidi gdk-pixbuf2 libpng libtiff libjpeg libepoxy
         libgl libegl harfbuzz libxkbcommon graphene iso-codes tracker3
         wayland libxrandr libx11 libxrender libxi libxext libxcursor
         libxdamage libxfixes fontconfig gst-plugins-bad-libs librsvg
         shared-mime-info desktop-file-utils mesa gtk-update-icon-cache
	)
	optdepends=(
		'libcups: printers in printing dialog'
		'dconf: default GSettings backend'
		'libcanberra: sounds events'
		'adwaita-icon-theme: default icon theme'
		'cantarell-fonts: default font'
	)

	DESTDIR="$pkgdir" meson install -C build

	install -Dt "$pkgdir/usr/share/gtk-4.0" -m644 settings.ini
	install -Dt "$pkgdir/usr/share/libalpm/hooks" -m644 gtk-query-immodules-4.0.hook

	rm "$pkgdir/usr/bin/gtk-update-icon-cache"
}

package_gtk-update-icon-cache() {
  pkgdesc="GTK icon cache updater"
  depends=(gdk-pixbuf2 librsvg)
  optdepends=(hicolor-icon-theme)

  mv guic/* "$pkgdir"
  ln -s gtk4-update-icon-cache "$pkgdir/usr/bin/gtk-update-icon-cache"
  ln -s gtk4-update-icon-cache.1 "$pkgdir/usr/share/man/man1/gtk-update-icon-cache.1"

  install -Dt "$pkgdir/usr/share/libalpm/hooks" -m644 gtk-update-icon-cache.hook
  install -D gtk-update-icon-cache.script "$pkgdir/usr/share/libalpm/scripts/gtk-update-icon-cache"
}
