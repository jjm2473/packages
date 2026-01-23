include $(TOPDIR)/rules.mk

PKG_NAME:=websockify
PKG_VERSION:=0.13.0
PKG_RELEASE:=1

PYPI_NAME:=websockify
PKG_HASH:=9969731116653226c4c499a8a50712c8f3f7c105c615ead7ebc6ae781f0ba954

PKG_LICENSE:=LGPL-3.0-only
PKG_LICENSE_FILES:=COPYING
PKG_MAINTAINER:=jjm2473 <jjm2473@gmail.com>

include ../../lang/python/pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../../lang/python/python3-package.mk

define Package/websockify
  SECTION:=net
  CATEGORY:=Network
  TITLE:=websockify
  URL:=https://github.com/novnc/websockify
  DEPENDS:= \
    +python3-numpy \
    +python3-requests \
    +python3-jwcrypto \
    +python3-redis
endef

define Package/websockify/description
  websockify: WebSockets support for any application/server
endef

$(eval $(call Py3Package,websockify))
$(eval $(call BuildPackage,websockify))
$(eval $(call BuildPackage,websockify-src))
