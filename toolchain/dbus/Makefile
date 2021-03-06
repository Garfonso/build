NAME     = dbus
TITLE    = dbus
SRC_VER  = 1.4.16
VERSION  = 1.4.16-2
APP_ID   = org.webosinternals.toolchain.${NAME}
HOMEPAGE = http://dbus.freedesktop.org
MAINTAINER = Will Rea <will.rea@gmail.com>
DESCRIPTION = Freedesktop.org message bus system
TYPE	 = Linux Daemon
CATEGORY = Utilities
ICON	 = http://www.webos-internals.org/images/a/ae/Icon_WebOSInternals_Base_Patch.png
LICENSE  = GPL v2 / AFL 2.1
MINWEBOSVERSION = 2.0
CHANGELOG = \
1.4.16-1 Initial Release

SRC_TGZ = http://dbus.freedesktop.org/releases/dbus/dbus-${SRC_VER}.tar.gz

.PHONY: package
package: ipkgs/${APP_ID}_${VERSION}_armv7.ipk ipkgs/${APP_ID}_${VERSION}_i686.ipk

include ../../support/package.mk

include ../../support/download.mk

.PHONY: stage
stage: build/.staged-${VERSION}

build/.staged-${VERSION}: build/armv7.staged-${VERSION} build/armv6.staged-${VERSION} build/i686.staged-${VERSION}
	touch $@

build/%.staged-${VERSION} : build/%.built-${VERSION}
	mkdir -p ../../staging/$*/usr/include
	cp -rp build/$*/usr/include/* ../../staging/$*/usr/include/
	mkdir -p ../../staging/$*/usr/lib
	cp -rp build/$*/usr/lib/* ../../staging/$*/usr/lib/
	touch $@

.PHONY: unpack
unpack: build/.unpacked-${VERSION}

build/.unpacked-${VERSION}: ${DL_DIR}/${NAME}-${VERSION}.tar.gz
	rm -rf build
	mkdir -p build/armv7 build/i686
	tar -C build -xf ${DL_DIR}/${NAME}-${VERSION}.tar.gz
	mv build/${NAME}-${SRC_VER} build/src

	touch $@

.PHONY: build
build: build/.built-${VERSION}

build/.built-${VERSION}: build/armv7.built-${VERSION}
	touch $@

include ../../support/cross-compile.mk
installpkgdir=${PWD}/build/$*

build/%.built-${VERSION}: build/.unpacked-${VERSION}
	( cd build/src ; \
	CC="${CROSS_COMPILE_$*}gcc" \
	LDFLAGS="-Wl,-L${STAGING_DIR_$*}/usr/lib,-rpath-link,${STAGING_DIR_$*}/usr/lib -L${STAGING_DIR_$*}/usr/lib" \
	CFLAGS="-I${STAGING_DIR_$*}/usr/include" \
	PKG_CONFIG_PATH="${STAGING_DIR_$*}/usr/lib/pkgconfig" \
	GLIB_CFLAGS="-I${STAGING_DIR_$*}/usr/include/glib-2.0" \
	GLIB_LIBS="-L${STAGING_DIR_$*}/usr/lib -lglib-2.0" \
	./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var --with-dbus-user=root \
		--with-system-socket="/var/run/dbus/system_bus_socket" \
		--with-system-pid-file="/var/run/dbus.pid" \
		--with-dbus-user=root \
		--disable-tests \
		--disable-console-owner-file \
		--without-x --with-xml=expat \
		--host=${CONFIGURE_HOST_$*}; \
	${MAKE} clean all )
	mkdir -p build/$*/usr/lib/pkgconfig
	mkdir -p build/$*/usr/include/dbus-1.0/dbus
	install -m 755 build/src/dbus/.libs/libdbus-1.a build/$*/usr/lib
	install -m 755 build/src/dbus/.libs/*.so* build/$*/usr/lib
	install -m 755 build/src/dbus-1.pc build/$*/usr/lib/pkgconfig
	install -m 644 build/src/dbus/*.h build/$*/usr/include/dbus-1.0/dbus
	touch $@

clobber::
	rm -rf build
