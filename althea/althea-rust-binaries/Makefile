#
# Copyright (C) 2017-2018 Justin Kilpatrick, Stanislaw Drozd, Openwrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

# you must set $RUST_TRIPLE in your env
include $(TOPDIR)/rules.mk

PKG_NAME:=althea-rust-binaries
PKG_VERSION:=0.3.6
PKG_RELEASE:=0

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/althea-mesh/althea_rs
PKG_SOURCE_VERSION:=master
PKG_LICENSE:=Apache-2.0

include $(INCLUDE_DIR)/package.mk

define Package/althea-rust-binaries
  SECTION:=net
  CATEGORY:=Utilities
  SUBMENU:=Althea
  TITLE:=Althea Rust executables
  URL:=https://github.com/althea-mesh/althea_rs
  MAINTAINER:= Justin Kilpatrick <justin@altheamesh.com>
  DEPENDS:=@IPV6 +libopenssl +libsqlite3 +ipset +kmod-wireguard +wireguard
endef

define Package/althea-rust-binaries/description
  Althea Rust executables
endef

ifeq ($(ARCH),arm)
	CROSS_PREFIX:=arm-openwrt-linux-muslgnueabi
	TARGET_LD:=arm-openwrt-linux-muslgnueabi-ld
	TARGET_AR:=arm-openwrt-linux-muslgnueabi-ar
else ifeq ($(ARCH),mips)
	CROSS_PREFIX:=mips-openwrt-linux-musl
	TARGET_LD:=mips-openwrt-linux-musl-ld
	TARGET_AR:=mips-openwrt-linux-musl-ar
else ifeq ($(ARCH),mips64)
	CROSS_PREFIX:=mips64-openwrt-linux
	TARGET_LD:=mips64-openwrt-linux-ld
	TARGET_AR:=mips64-openwrt-linux-ar
else ifeq ($(ARCH),mipsel)
	CROSS_PREFIX:=mipsel-openwrt-linux-musl
	TARGET_LD:=mipsel-openwrt-linux-musl-ld
	TARGET_AR:=mipsel-openwrt-linux-musl-ar
else ifeq ($(ARCH),x86_64)
	CROSS_PREFIX:=x86_64-openwrt-linux-musl
	TARGET_LD:=x86_64-openwrt-linux-musl-ld
	TARGET_AR:=x86_64-openwrt-linux-musl-ar
endif

RITA_PATH:="$(PKG_BUILD_DIR)/target/$(RUST_TRIPLE)/release/rita"

define Build/Compile
	(\
		cd $(PKG_BUILD_DIR)/rita && \
\
		OPENSSL_DIR=$(STAGING_DIR)/usr/ \
		RUSTFLAGS="-C linker=$(TARGET_CC_NOCACHE) -C ar=$(TARGET_AR)" \
		TARGET=$(RUST_TRIPLE) \
		TARGET_CC=$(TARGET_CC_NOCACHE) \
		TARGET_CXX=$(TARGET_CXX_NOCACHE) \
		PKG_CONFIG_ALLOW_CROSS=1 \
		SQLITE3_LIB_DIR="$(STAGING_DIR)/usr/lib/" \
		CROSS_COMPILE=$(CROSS_PREFIX) \
		OPENSSL_STATIC=1 \
\
		cargo build --release --target $(RUST_TRIPLE) \
	)
	$(STRIP) $(RITA_PATH)
endef

define Package/althea-rust-binaries/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(RITA_PATH) $(1)/usr/sbin/rita
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/rita.init $(1)/etc/init.d/rita
endef

$(eval $(call BuildPackage,althea-rust-binaries))
