#
# Copyright (C) 2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=rkrga
PKG_VERSION:=1.3.1
PKG_RELEASE:=11

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_VERSION:=1e2f0dbb838de3512a8a29143196d7b5e460e1ca
PKG_SOURCE_URL_FILE:=$(PKG_SOURCE_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/JeffyCN/rockchip_mirrors/archive/
PKG_HASH:=5fd33d0df4b908830c36b3501cf1cc79c2a43b73dd9c744edd299982fb0dddc3

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/rkrga
  SECTION:=multimedia
  CATEGORY:=Multimedia
  TITLE:=Rockchip RGA
  DEPENDS:=@aarch64 +libstdcpp +libdrm
  URL:=https://github.com/JeffyCN/rockchip_mirrors/tree/linux-rga
endef

define Package/rkrga/description
 Rockchip RGA (Raster Graphic Acceleration Unit)
 is an independent 2D hardware accelerator that can be used to accelerate point/line drawing and perform common 2D graphics operations such as image scaling, rotation, bitBlt, and alpha blending.The code in this repository implements the RGA user space driver and provides a series of 2D graphics operation APIs.
endef

define Package/rkrga-demo
$(call Package/rkrga)
  TITLE+= Demo
  DEPENDS:=+rkrga
endef

define Package/rkrga-demo/description
$(call Package/rkrga/description)

This package provides the demo.
endef

CMAKE_OPTIONS += -DCMAKE_BUILD_TYPE=Release \
	-DCMAKE_BUILD_TARGET=cmake_linux \
	-DBUILD_WITH_LIBDRM=true

TARGET_CFLAGS+= -I$(STAGING_DIR)/usr/include/libdrm

define Build/Prepare
	rm -rf $(PKG_BUILD_DIR)/
	mkdir -p $(PKG_BUILD_DIR)/
	$(TAR) -xzf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR) --strip-components=1
	$(Build/Patch)
endef

define Package/rkrga/install
	$(INSTALL_DIR) $(1)/usr
	$(CP) $(PKG_INSTALL_DIR)/usr/lib $(1)/usr/
endef

define Package/rkrga-demo/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(PKG_INSTALL_DIR)/usr/bin/rgaImDemo $(1)/usr/bin/
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include $(1)/usr/include/rga
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/librga.so $(1)/usr/lib/
endef

$(eval $(call BuildPackage,rkrga))
$(eval $(call BuildPackage,rkrga-demo))
