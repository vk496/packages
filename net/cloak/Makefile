include $(TOPDIR)/rules.mk

PKG_NAME:=Cloak
PKG_VERSION:=2.10.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/cbeuw/$(PKG_NAME)/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=f9fcc78e967184421b415309cedf3bf9b2a880827bbc20890240dff4aa68eb12

PKG_MAINTAINER:=Valentin Kivachuk Burda <vk18496@gmail.com>
PKG_LICENSE:=GPL-3.0-only
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/cbeuw/$(PKG_NAME)
GO_PKG_LDFLAGS_X:=main.Version=$(PKG_VERSION)
GO_PKG_BUILD_PKG:=$(GO_PKG)/cmd/ck-server $(GO_PKG)/cmd/ck-client

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/cloak
  SECTION:=net
  CATEGORY:=Network
  TITLE:=A censorship circumvention tool/proxy
  URL:=https://github.com/cbeuw/Cloak
  DEPENDS:=$(GO_ARCH_DEPENDS)
endef

define Package/cloak/conffiles
/etc/config/cloak
/etc/cloak/ckclient.json
/etc/cloak/ckserver.json
endef


define Package/cloak/install
  $(call GoPackage/Package/Install/Bin,$(1))

  $(INSTALL_DIR) $(1)/etc/config
  $(INSTALL_CONF) ./files/cloak.config $(1)/etc/config/cloak

  $(INSTALL_DIR) $(1)/etc/init.d
  $(INSTALL_BIN) ./files/cloak.init $(1)/etc/init.d/cloak

  $(INSTALL_DIR) $(1)/etc/cloak
  $(INSTALL_CONF) ./files/ckclient.json $(1)/etc/cloak/ckclient.json
  $(INSTALL_CONF) ./files/ckserver.json $(1)/etc/cloak/ckserver.json
endef

define Package/cloak/postinst
#!/bin/sh

# Empty var means we are on target system
# instead of host machine during build

if [ -z "$${IPKG_INSTROOT}" ]; then
	echo "Generating client & server keys"

	pub_priv=$$(ck-server -k)
  pub=$$(echo $${pub_priv} | cut -d, -f1)
  priv=$$(echo $${pub_priv} | cut -d, -f1)
  uid=$$(ck-server -u)

  echo "Installing (if needed) keys into /etc/cloak json config"

  sed -i -e "s/---UID---/$${uid}/g" /etc/cloak/ckclient.json
  sed -i -e "s/---UID---/$${uid}/g" /etc/cloak/ckserver.json

  sed -i -e "s/---PRIVATE---/$${priv}/g" /etc/cloak/ckserver.json
  sed -i -e "s/---PUBLIC---/$${pub}/g" /etc/cloak/ckclient.json

fi
exit 0
endef

$(eval $(call GoBinPackage,cloak))
$(eval $(call BuildPackage,cloak))
