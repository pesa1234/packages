include $(TOPDIR)/rules.mk

PKG_NAME:=nordvpnlite
PKG_RELEASE:=2

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/NordSecurity/libtelio.git
PKG_SOURCE_VERSION:=7bcdb64e944aeeef731b6c9dc3da2421093ed12a
PKG_SOURCE_DATE:=2026-04-17
PKG_MIRROR_HASH:=54d046af698b08cd62abb5dc19b6d2cfbfd64f6a6fe02c76bd0d6d79c1d5fdae

MAKE_PATH:=clis/nordvpnlite

PKG_MAINTAINER:=Lukas Pukenis <lukas.pukenis@nordsec.com>, \
	Oleksandr Oliinyk <oleksandr.oliinyk@nordsec.com>, \
	Tomasz Grzegowski <tomasz.grzegowski@nordsec.com>
PKG_LICENSE:=GPL-3.0-only
PKG_LICENSE_FILES:=LICENSE clis/nordvpnlite/openwrt/feed/net/nordvpnlite/THIRD-PARTY-NOTICES
PKG_BUILD_DEPENDS:=rust/host protobuf/host
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include ../../lang/rust/rust-package.mk

# Upstream build.rs checks for git hooks which don't exist in the build directory
CARGO_PKG_VARS += BYPASS_LLT_SECRETS=1
# Short commit hash for the libtelio snapshot above
CARGO_PKG_VARS += LIBTELIO_COMMIT_SHA=7bcdb64e

define Package/nordvpnlite
  SECTION:=net
  CATEGORY:=Network
  TITLE:=NordVPN Lite
  URL:=https://www.nordsecurity.com
  DEPENDS:=$(RUST_ARCH_DEPENDS) @!i386 +kmod-tun +kmod-wireguard
endef

define Package/nordvpnlite/description
  NordVPN Lite is the gateway to a secure and private access to the internet.
endef

define Package/nordvpnlite/conffiles
/etc/nordvpnlite/config.json
/etc/config/nordvpnlite
endef

define Package/nordvpnlite/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/nordvpnlite $(1)/usr/sbin/

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/nordvpnlite.init $(1)/etc/init.d/nordvpnlite

	$(INSTALL_DIR) $(1)/etc/nordvpnlite
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/clis/nordvpnlite/openwrt/feed/net/nordvpnlite/files/config.json \
		$(1)/etc/nordvpnlite/config.json

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/nordvpnlite.conf $(1)/etc/config/nordvpnlite
endef

define Package/nordvpnlite/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	echo "Enabling nordvpnlite service"
	/etc/init.d/nordvpnlite enable
fi
exit 0
endef

define Package/nordvpnlite/prerm
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	echo "Disabling nordvpnlite service"
	/etc/init.d/nordvpnlite disable
fi
exit 0
endef

$(eval $(call RustBinPackage,nordvpnlite))
$(eval $(call BuildPackage,nordvpnlite))
