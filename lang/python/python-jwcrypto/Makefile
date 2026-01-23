#
# Copyright (C) 2015-2016, 2018-2020, 2023 Jeffery To
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python-jwcrypto
PKG_VERSION:=1.5.6
PKG_RELEASE:=1

PYPI_NAME:=jwcrypto
PKG_HASH:=771a87762a0c081ae6166958a954f80848820b2ab066937dc8b8379d65b1b039

PKG_LICENSE:=LGPL-3.0-only
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=jjm2473 <jjm2473@gmail.com>

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-jwcrypto
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=jwcrypto library
  URL:=https://github.com/latchset/jwcrypto
  DEPENDS:=+python3-typing-extensions +python3-cryptography
endef

define Package/python3-jwcrypto/description
An implementation of the JOSE Working Group documents
endef

$(eval $(call Py3Package,python3-jwcrypto))
$(eval $(call BuildPackage,python3-jwcrypto))
$(eval $(call BuildPackage,python3-jwcrypto-src))
