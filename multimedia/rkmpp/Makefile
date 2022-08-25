#
# Copyright (C) 2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=rkmpp
PKG_VERSION:=2022-08-05
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_VERSION:=7b380ccab68384ffcab05da5359c25c703db5471
PKG_SOURCE_URL_FILE:=$(PKG_SOURCE_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/JeffyCN/rockchip_mirrors/archive/
PKG_HASH:=809bb0d2329b37dce51f96afc99d827853ca164f29cc04f6926c2384d2176ec9

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/rkmpp
  SECTION:=multimedia
  CATEGORY:=Multimedia
  TITLE:=Rockchip MPP
  DEPENDS:=@aarch64 +libstdcpp
  URL:=https://github.com/JeffyCN/rockchip_mirrors/tree/mpp
endef

define Package/rkmpp/description
 Rockchip Media Process Platform (MPP)
endef


define Package/rkmpp-demo
$(call Package/rkmpp)
  TITLE+= Demo
  DEPENDS:=+rkmpp
endef

define Package/rkmpp-demo/description
$(call Package/rkmpp/description)

This package provides the demo.
endef

CMAKE_OPTIONS += -DCMAKE_BUILD_TYPE=Release \
	-DHAVE_DRM=ON

define Build/Prepare
	rm -rf $(PKG_BUILD_DIR)/
	mkdir -p $(PKG_BUILD_DIR)/
	$(TAR) -xzf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR) --strip-components=1
	$(Build/Patch)
endef

define Package/rkmpp/install
	$(INSTALL_DIR) $(1)/usr
	$(CP) $(PKG_INSTALL_DIR)/usr/lib $(1)/usr/
endef

define Package/rkmpp-demo/install
	$(INSTALL_DIR) $(1)/usr
	$(CP) $(PKG_INSTALL_DIR)/usr/bin $(1)/usr/
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/rockchip $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib $(1)/usr/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/*.pc $(1)/usr/lib/pkgconfig/
endef

$(eval $(call BuildPackage,rkmpp))
$(eval $(call BuildPackage,rkmpp-demo))
