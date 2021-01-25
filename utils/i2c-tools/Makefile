#
# Copyright (C) 2007-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

include $(TOPDIR)/rules.mk

PKG_NAME:=i2c-tools
PKG_VERSION:=4.2
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@KERNEL/software/utils/i2c-tools
PKG_HASH:=37f2dabc7082d185903ff21d1f584b5dcb4dd2eb2c879bbd8d7c50ae900dacd6

PKG_MAINTAINER:=Daniel Golle <daniel@makrotopia.org>
PKG_LICENSE:=GPL-2.0-or-later LGPL-2.1-or-later
PKG_LICENSE_FILES:=COPYING COPYING.LGPL

include $(INCLUDE_DIR)/package.mk
$(call include_mk, python-package.mk)

define Package/i2c/Default
  URL:=https://i2c.wiki.kernel.org/index.php/I2C_Tools
  TITLE:=I2C
endef

define Package/libi2c
  $(call Package/i2c/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE+=library for i2c-tools
endef

define Package/i2c-tools
  $(call Package/i2c/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE+=tools for Linux
  DEPENDS:=+libi2c
endef

define Package/python-smbus
  $(call Package/i2c/Default)
  SUBMENU:=Python
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Python bindings for the SMBUS
  DEPENDS:=+libi2c +python-light
endef

define Package/i2c-tools/description
 This package contains an heterogeneous set of I2C tools for Linux. These tools
 were originally part of the lm-sensors package.
endef

define Package/python-smbus/description
 This package contain the python bindings for Linux SMBus access through i2c-dev.
endef

TARGET_CPPFLAGS += -I$(PKG_BUILD_DIR)/include

ifdef CONFIG_PACKAGE_python-smbus
  define Build/Compile/python-smbus
	$(if $(Build/Compile/PyMod),,@echo Python packaging code not found.; false)
	$(call Build/Compile/PyMod,./py-smbus/, \
		install --prefix="$(PKG_INSTALL_DIR)/usr", \
	)
  endef
endif

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		LINUX="$(LINUX_DIR)" \
		CC="$(TARGET_CC)" \
		STAGING_DIR="$(STAGING_DIR)" \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		CFLAGS="$(TARGET_CFLAGS)"
	$(Build/Compile/python-smbus)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include $(1)/usr/lib
	$(CP) $(PKG_BUILD_DIR)/include/i2c $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/lib/libi2c.{a,so*} $(1)/usr/lib/
endef

define Package/libi2c/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_BUILD_DIR)/lib/libi2c.so* $(1)/usr/lib/
endef

define Package/i2c-tools/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/tools/i2cdetect $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/tools/i2cdump $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/tools/i2cset $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/tools/i2cget $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/tools/i2ctransfer $(1)/usr/sbin/
endef

define PyPackage/python-smbus/filespec
+|$(PYTHON_PKG_DIR)/smbus.so
endef

$(eval $(call BuildPackage,libi2c))
$(eval $(call BuildPackage,i2c-tools))
$(eval $(call PyPackage,python-smbus))
$(eval $(call BuildPackage,python-smbus))
