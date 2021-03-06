#
# Copyright (C) 2006-2012 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.

include $(TOPDIR)/rules.mk
# include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=opkg
PKG_REV:=618
PKG_VERSION:=$(PKG_REV)
PKG_MD5SUM:=d6087a26ff5cf6b86bd159e8aca9f650
PKG_RELEASE:=5
PKG_LICENSE:=GPLv2

PKG_SOURCE_PROTO:=svn
PKG_SOURCE_VERSION:=$(PKG_REV)
PKG_SOURCE_SUBDIR:=opkg-$(PKG_VERSION)
PKG_SOURCE_URL:=http://opkg.googlecode.com/svn/trunk/
PKG_SOURCE:=$(PKG_SOURCE_SUBDIR).tar.gz
PKG_FIXUP:=autoreconf
PKG_REMOVE_FILES = autogen.sh aclocal.m4

PKG_BUILD_DEPENDS += opkg/host
PKG_BUILD_PARALLEL:=1
HOST_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/host-build.mk

define Package/opkg
  SECTION:=base
  CATEGORY:=Base system
  TITLE:=opkg package management system
  MAINTAINER:=Jo-Philipp Wich <xm@subsignal.org>
  DEPENDS:=+PACKAGE_opkg-install:base-files +PACKAGE_opkg-install:libc +PACKAGE_opkg-install:libgcc +PACKAGE_opkg-install:librt +PACKAGE_opkg-install:libpthread +PACKAGE_opkg-install:librpc
  DEPENDS+=+PACKAGE_opkg-install:busybox +PACKAGE_opkg-install:dropbear +PACKAGE_opkg-install:libpam +PACKAGE_opkg-install:libndm +PACKAGE_opkg-install:pam_ndm
  URL:=http://wiki.openmoko.org/wiki/Opkg
endef

define Package/opkg/description
  Lightweight package management system
  opkg is the opkg Package Management System, for handling
  installation and removal of packages on a system. It can
  recursively follow dependencies and download all packages
  necessary to install a particular package.

  opkg knows how to install both .ipk and .deb packages.
endef

TARGET_CFLAGS += $(if $(CONFIG_GCC_VERSION_4_3)$(CONFIG_GCC_VERSION_4_4),-Wno-array-bounds)
TARGET_CFLAGS += -ffunction-sections -fdata-sections
EXTRA_CFLAGS += $(TARGET_CPPFLAGS)

CONFIGURE_ARGS += \
	--disable-curl \
	--disable-gpg \
	--with-opkglibdir=/usr/lib \
	--with-opkgetcdir=/opt/etc \
	--with-opkglockfile=/tmp/opkg.lock

MAKE_FLAGS = \
		CC="$(TARGET_CC)" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		HOST_CPU="$(PKGARCH)" \
		LDFLAGS="-Wl,--gc-sections" \

define Package/opkg/install
	$(INSTALL_DIR) $(1)/usr/lib/opkg
	$(INSTALL_DIR) $(1)/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/opkg-cl $(1)/bin/opkg
endef

define Build/InstallDev
	mkdir -p $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/libopkg $(1)/usr/include/
endef


HOST_CONFIGURE_ARGS+= \
	--disable-curl \
	--disable-gpg \
	--with-opkgetcdir=/etc \
	--with-opkglockfile=/tmp/opkg.lock

define Host/Compile
	+$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR) CC="$(HOSTCC)" all
endef

define Host/Install
	$(INSTALL_BIN) $(HOST_BUILD_DIR)/src/opkg-cl $(STAGING_DIR_HOST)/bin/opkg
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,opkg))
