#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=u-boot
PKG_VERSION:=2024.04
PKG_RELEASE:=1

# Mainline U-Boot has no support for the SolidRun i.MX8M Plus SoM, so the
# vendor tree is used instead. It is NXP's lf_v2024.04 with SolidRun's board
# support on top, and builds flash.bin through binman like the other i.MX8MP
# boards in this target.
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/SolidRun/u-boot.git
PKG_SOURCE_DATE:=2026-08-09
PKG_SOURCE_VERSION:=40374c3191f459343404be5b67771b753486ac50
PKG_MIRROR_HASH:=skip

include $(INCLUDE_DIR)/u-boot.mk
include $(INCLUDE_DIR)/package.mk

# This builds U-Boot proper and the SPL, but not a bootable flash.bin.
# Assembling that needs spl/u-boot-spl-ddr.bin, which nothing in the vendor
# tree produces: NXP builds it out of tree with imx-mkimage, which is not
# packaged here yet. Until it is, the pieces imx-mkimage consumes are staged
# and the board keeps booting the imx-boot already in its eMMC/SPI, so the
# package is not selected by the device.
define U-Boot/Default
  BUILD_TARGET:=imx
  BUILD_SUBTARGET:=cortexa53
  DEPENDS:=+firmware-imx +trusted-firmware-a-imx8mp
  ATF:=bl31_imx8mp.bin
endef

define U-Boot/imx8mp_solidrun
  NAME:=SolidRun i.MX8M Plus SoM
endef

UBOOT_TARGETS := \
	imx8mp_solidrun

UBOOT_CUSTOMIZE_CONFIG := \
	--disable TOOLS_MKEFICAPSULE

UBOOT_MAKE_FLAGS += \
	BL31=$(STAGING_DIR_IMAGE)/$(ATF) \
	BINMAN_INDIRS="$(STAGING_DIR_IMAGE)/firmware-imx $(STAGING_DIR_IMAGE)"

define Build/InstallDev
	$(INSTALL_DIR) $(STAGING_DIR_IMAGE)
	$(CP) $(PKG_BUILD_DIR)/u-boot.bin \
		$(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-u-boot.bin
	$(CP) $(PKG_BUILD_DIR)/spl/u-boot-spl.bin \
		$(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-u-boot-spl.bin
endef

define Package/u-boot/install/default
endef

$(eval $(call BuildPackage/U-Boot))
