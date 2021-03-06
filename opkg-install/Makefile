#
# Copyright (C) 2015 NDM Systems, McMCC
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=opkg-install
PKG_RELEASE:=2
PKG_VERSION:=1.2
VER:=$(LINUX_VERSION)-$(LINUX_RELEASE)
FULL_VER:=$(VER)-$(LINUX_VERMAGIC)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/version.mk

PKG_BUILD_DEPENDS += opkg/host

define Package/opkg-install
  SECTION:=base
  CATEGORY:=Base system
  URL:=http://keenopt.ru/
#  NAME:=opkg-install
  TITLE:=Preinstall opkg config and data base.
  DEPENDS:=PACKAGE_opkg:opkg
  PKGARCH:=$(SUBTARGET)
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
endef

define Build/Compile

ifneq ($(CONFIG_PACKAGE_opkg),)
	cd $(PKG_BUILD_DIR); \
	mkdir -p $(PKG_BUILD_DIR)/info; \
	IPKG_FILES="base-files libc libgcc librt libpthread librpc busybox dropbear libpam libndm pam_ndm opkg"; \
	for ipkg in $$$$IPKG_FILES; do \
		tar xzf `ls $(PACKAGE_DIR)/$$$${ipkg}_*` ./control.tar.gz ./data.tar.gz -C $(PKG_BUILD_DIR); \
		tar xzf control.tar.gz ./control; \
		grep -e "^Package:" -e "^Version:" -e "^Depends:" -e "^Provides:" control >> $(PKG_BUILD_DIR)/status; \
		echo "Status: install ok installed" >> $(PKG_BUILD_DIR)/status; \
		echo "Essential: yes" >> $(PKG_BUILD_DIR)/status; \
		grep -e "^Architecture:" control >> $(PKG_BUILD_DIR)/status; \
		tar xzf control.tar.gz ./conffiles 2> /dev/null; \
		if [ -f ./conffiles ]; then \
			echo "Conffiles:" >> $(PKG_BUILD_DIR)/status; \
			for conf in `cat ./conffiles`; do \
				CMD5=`tar xzf data.tar.gz .$$$$conf -O | md5sum | awk -F" " '{print $$$$1}'`; \
				echo " /opt$$$$conf $$$$CMD5" >> $(PKG_BUILD_DIR)/status; \
			done; \
			mv -f ./conffiles $(PKG_BUILD_DIR)/info/$$$${ipkg}.conffiles; \
		fi; \
		echo "Installed-Time: 1445514529" >> $(PKG_BUILD_DIR)/status; \
		echo "Auto-Installed: yes" >> $(PKG_BUILD_DIR)/status; \
		echo >> $(PKG_BUILD_DIR)/status; \
		mv -f control $(PKG_BUILD_DIR)/info/$$$${ipkg}.control; \
		tar tvzf data.tar.gz --show-transformed-names | grep -v "^d.*" | awk -F" " '{print $$$$6}' | sed -e 's#^./#/opt/#g' > $(PKG_BUILD_DIR)/info/$$$${ipkg}.list; \
		rm -f control.tar.gz data.tar.gz; \
	done;
else
	mkdir -p $(PKG_BUILD_DIR)/info; \
	$(CP) ./base-files/status_$(ARCH) $(PKG_BUILD_DIR)/status; \
	$(CP) ./base-files/info_$(ARCH)/* $(PKG_BUILD_DIR)/info/;
endif

endef

define Package/opkg-install/install
	$(INSTALL_DIR) $(1)/usr/lib/opkg/info
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_DATA) ./configs/opkg.conf $(1)/etc/
	$(VERSION_SED) $(1)/etc/opkg.conf
	$(CP) ./files/* $(1)/usr/lib/opkg
	for ipkg in `ls $(1)/usr/lib/opkg/info/*.control`; do \
		$(SED) 's#@@VER@@#$(VER)#g' -e 's#@@FULL_VER@@#$(FULL_VER)#g' -e 's#@@BOARD@@#$(SUBTARGET)#g' $$$${ipkg}; \
	done
	$(CP) $(PKG_BUILD_DIR)/info/* $(1)/usr/lib/opkg/info
	cat $(PKG_BUILD_DIR)/status >> $(1)/usr/lib/opkg/status
	$(SED) 's#@@VER@@#$(VER)#g' -e 's#@@FULL_VER@@#$(FULL_VER)#g' -e 's#@@BOARD@@#$(SUBTARGET)#g' $(1)/usr/lib/opkg/status
	$(INSTALL_DIR) $(1)/lib/modules
	ln -sf $(LINUX_VERSION) $(1)/lib/modules/current
endef

$(eval $(call BuildPackage,opkg-install))
