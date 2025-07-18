# SPDX-License-Identifier: GPL-2.0-only

include $(TOPDIR)/rules.mk

PKG_NAME:=alfred
PKG_VERSION:=2025.2
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://downloads.open-mesh.org/batman/releases/batman-adv-$(PKG_VERSION)
PKG_HASH:=4347d1e4b0794afbf6b171a857b9a3c7d51a291fc54f16cdad92c7f7d1cdb51a

PKG_MAINTAINER:=Simon Wunderlich <sw@simonwunderlich.de>
PKG_LICENSE:=GPL-2.0-only MIT
PKG_LICENSE_FILES:=LICENSES/preferred/GPL-2.0 LICENSES/preferred/MIT

PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=gc-sections lto

PKG_CONFIG_DEPENDS += \
	CONFIG_ALFRED_NEEDS_lua \
	CONFIG_ALFRED_NEEDS_libgps \
	CONFIG_PACKAGE_ALFRED_VIS \
	CONFIG_PACKAGE_ALFRED_BATHOSTS \
	CONFIG_PACKAGE_ALFRED_GPSD

include $(INCLUDE_DIR)/package.mk

define Package/alfred
  SECTION:=net
  CATEGORY:=Network
  TITLE:=A.L.F.R.E.D. - Almighty Lightweight Fact Remote Exchange Daemon
  URL:=https://www.open-mesh.org/
  DEPENDS:= +libc @IPV6 +libnl-tiny +librt \
            +ALFRED_NEEDS_lua:lua \
            +ALFRED_NEEDS_libgps:libgps
endef

define Package/alfred/description
  alfred is a user space daemon for distributing arbitrary local information
  over the mesh/network in a decentralized fashion. This data can be anything
  which appears to be useful - originally designed to replace the batman-adv
  visualization (vis), you may distribute hostnames, phone books, administration
  information, DNS information, the local weather forecast ...

  alfred runs as daemon in the background of the system. A user may insert
  information by using the alfred binary on the command line, or use special
  programs to communicate with alfred (done via unix sockets). alfred then takes
  care of distributing the local information to other alfred servers on other
  nodes. This is done via IPv6 link-local multicast, and does not require any
  configuration. A user can request data from alfred, and will receive the
  information available from all alfred servers in the network.
endef

define Package/alfred/conffiles
/etc/config/alfred
endef

define Package/alfred/config
	source "$(SOURCE)/Config.in"
endef

MAKE_FLAGS += \
	CONFIG_ALFRED_VIS=$(if $(CONFIG_PACKAGE_ALFRED_VIS),y,n) \
	CONFIG_ALFRED_GPSD=$(if $(CONFIG_PACKAGE_ALFRED_GPSD),y,n) \
	CONFIG_ALFRED_CAPABILITIES=n \
        LIBNL_NAME="libnl-tiny" \
        LIBNL_GENL_NAME="libnl-tiny" \
        REVISION="$(PKG_VERSION)-openwrt-$(PKG_RELEASE)"

define Package/alfred/install
	$(INSTALL_DIR) $(1)/usr/sbin
	cp -fpR  $(PKG_BUILD_DIR)/alfred $(1)/usr/sbin/
	[ "x$(CONFIG_PACKAGE_ALFRED_VIS)" == "xy" ] && cp -fpR  $(PKG_BUILD_DIR)/vis/batadv-vis $(1)/usr/sbin/ ; true
	[ "x$(CONFIG_PACKAGE_ALFRED_GPSD)" == "xy" ] && cp -fpR  $(PKG_BUILD_DIR)/gpsd/alfred-gpsd $(1)/usr/sbin/ ; true
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/alfred.init $(1)/etc/init.d/alfred
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) ./files/alfred.config $(1)/etc/config/alfred
	$(INSTALL_DIR) $(1)/etc/alfred
	[ "x$(CONFIG_PACKAGE_ALFRED_BATHOSTS)" == "xy" ] && $(INSTALL_BIN) ./files/bat-hosts.lua $(1)/etc/alfred/bat-hosts.lua ; true
endef

$(eval $(call BuildPackage,alfred))
