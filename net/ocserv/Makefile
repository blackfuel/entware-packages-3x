#
# Copyright (C) 2007-2011 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=ocserv
PKG_VERSION:=0.11.10
PKG_RELEASE:=1
PKG_USE_MIPS16:=0

PKG_BUILD_DIR :=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=ftp://ftp.infradead.org/pub/ocserv/
PKG_HASH:=52c821dcd234a8f0d61f440ce3c80d0b23051d7fbd74c6d47dc2b884ae25966a

PKG_LICENSE:=GPLv2+
PKG_LICENSE_FILES:=COPYING
PKG_FIXUP:=autoreconf

PKG_CONFIG_DEPENDS:= \
	CONFIG_OCSERV_PAM \
	CONFIG_OCSERV_SECCOMP \
	CONFIG_OCSERV_PROTOBUF \

include $(INCLUDE_DIR)/package.mk

define Package/ocserv/config
	source "$(SOURCE)/Config.in"
endef

define Package/ocserv
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=VPN
  TITLE:=OpenConnect VPN server
  URL:=http://www.infradead.org/ocserv/
  MAINTAINER:=Nikos Mavrogiannopoulos <n.mavrogiannopoulos@gmail.com>
  DEPENDS:= +OCSERV_RADIUS:libradcli +OCSERV_HTTP_PARSER:libhttp-parser +OCSERV_SECCOMP:libseccomp +libgnutls +certtool +libncurses +libreadline +OCSERV_PAM:libpam +OCSERV_PROTOBUF:libprotobuf-c +libev
  USERID:=ocserv=72:ocserv=72
endef

define Package/ocserv/description
 OpenConnect server (ocserv) is an SSL VPN server. Its purpose is to be
 a secure, small, fast and configurable VPN server. It implements the
 OpenConnect SSL VPN protocol, and has also (currently experimental)
 compatibility with clients using the AnyConnect SSL VPN protocol. The
 OpenConnect VPN protocol uses the standard IETF security protocols such
 as TLS 1.2, and Datagram TLS to provide the secure VPN service.
endef

EXTRA_CPPFLAGS+=-I$(STAGING_DIR)/opt/include/readline/
EXTRA_LDFLAGS+=-lncurses

CONFIGURE_ARGS+= \
	--with-pager="" \
	--enable-local-libopts \
	--with-libreadline-prefix="$(STAGING_DIR)/" \
	--without-libnl \
	--without-lz4 \
	--without-gssapi \
	--with-libcrypt-prefix="$(STAGING_DIR)/" \
	--with-libev-prefix="$(STAGING_DIR)/" \
	--without-lz4 \
	--with-local-talloc \

ifneq ($(CONFIG_OCSERV_PAM),y)
CONFIGURE_ARGS += --without-pam
endif

ifneq ($(CONFIG_OCSERV_RADIUS),y)
CONFIGURE_ARGS += --without-radius
endif

ifneq ($(CONFIG_OCSERV_SECCOMP),y)
CONFIGURE_ARGS += --disable-seccomp
endif

ifneq ($(CONFIG_OCSERV_PROTOBUF),y)
CONFIGURE_ARGS += --without-protobuf
endif

ifneq ($(CONFIG_OCSERV_HTTP_PARSER),y)
CONFIGURE_ARGS += --without-http-parser
endif

define Package/ocserv/conffiles
/opt/etc/ocserv
endef

define Package/ocserv/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/ocserv $(1)/opt/sbin/
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/ocserv-fw $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/ocpasswd/ocpasswd $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/occtl/occtl $(1)/opt/bin/
	$(INSTALL_DIR) $(1)/opt/etc/ocserv
	$(INSTALL_CONF) ./files/ocserv.conf.template $(1)/opt/etc/ocserv/ocserv.conf.template
	$(INSTALL_CONF) ./files/ocserv.conf $(1)/opt/etc/ocserv/ocserv.conf
	$(INSTALL_DIR) $(1)/opt/etc/config
	$(INSTALL_CONF) ./files/config $(1)/opt/etc/config/ocserv
	$(INSTALL_DIR) $(1)/opt/lib/upgrade/keep.d
	$(INSTALL_DATA) ./files/ocserv.upgrade $(1)/opt/lib/upgrade/keep.d/ocserv
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S79ocserv $(1)/opt/etc/init.d
endef

$(eval $(call BuildPackage,ocserv))
