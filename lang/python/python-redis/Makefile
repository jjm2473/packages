#
# Copyright (C) 2015-2016, 2018-2020, 2023 Jeffery To
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python-redis
PKG_VERSION:=7.1.0
PKG_RELEASE:=1

PYPI_NAME:=redis
PKG_SOURCE:=redis-py-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL_FILE:=$(PYPI_NAME)-$(PKG_VERSION).tar.gz
PKG_HASH:=b1cc3cfa5a2cb9c2ab3ba700864fb0ad75617b41f01352ce5779dabf6d5f9c3c

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=jjm2473 <jjm2473@gmail.com>

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-redis
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=redis library
  URL:=https://github.com/redis/redis-py
  DEPENDS:=
endef

define Package/python3-redis/description
The Python interface to the Redis key-value store.
endef

$(eval $(call Py3Package,python3-redis))
$(eval $(call BuildPackage,python3-redis))
$(eval $(call BuildPackage,python3-redis-src))
