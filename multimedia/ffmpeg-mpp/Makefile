#
# Copyright (C) 2022 jjm2473 <jjm2473@gmail.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=ffmpeg-mpp
PKG_VERSION:=4.4.git
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_VERSION:=73d7bc2a30fecf91a78a7fb2c497e96c327c0fd0
PKG_SOURCE_URL_FILE:=$(PKG_SOURCE_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/JeffyCN/FFmpeg/archive/
PKG_HASH:=aa4b3ab5de6d498b051f4aa7634c3165cd1a08b23e9873fd2ef928e57f786979

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)

FFMPEG_PATENTED_DECODERS:= \
	atrac3 \
	h264 \
	hevc \
	vc1 \

FFMPEG_PATENTED_MUXERS:= \
	h264 \
	hevc \
	vc1 \

FFMPEG_PATENTED_DEMUXERS:=$(FFMPEG_PATENTED_MUXERS)

FFMPEG_PATENTED_PARSERS:= \
	h264 \
	hevc \
	vc1 \

include $(INCLUDE_DIR)/package.mk

define Package/ffmpeg/Default
 TITLE:=FFmpeg for rockchip SoC
 URL:=https://github.com/JeffyCN/FFmpeg
 DEPENDS+=@aarch64 @!ALL +libpthread
endef

define Package/ffmpeg/Default/description
 FFmpeg is a a software package that can record, convert and stream digital
 audio and video in numerous formats.

 FFmpeg licensing / patent issues are complex. It is the reponsibility of the
 user to understand any requirements in this regard with its usage. See:
 https://ffmpeg.org/legal.html for further information.

 This is a rockchip mpp variant.
endef

define Package/ffmpeg-mpp
$(call Package/ffmpeg/Default)
 SECTION:=multimedia
 CATEGORY:=Multimedia
 TITLE+= program
 DEPENDS+= +libffmpeg-mpp
 PROVIDES:= ffmpeg
endef

define Package/ffmpeg-mpp/description
$(call Package/ffmpeg/Default/description)
 .
 This package contains the FFmpeg command line tool.
endef

define Package/ffprobe-mpp
$(call Package/ffmpeg/Default)
 SECTION:=multimedia
 CATEGORY:=Multimedia
 TITLE+= CLI media identifier
 DEPENDS+= +libffmpeg-mpp
 PROVIDES:= ffprobe
endef

define Package/ffprobe-mpp/description
$(call Package/ffmpeg/Default/description)
 .
 This package contains the FFprobe command line tool.
endef

define Package/libffmpeg-mpp
$(call Package/ffmpeg/Default)
 SECTION:=libs
 CATEGORY:=Libraries
 TITLE+= libraries
 DEPENDS+= +zlib +libbz2
 PROVIDES:= libffmpeg
 TITLE+= (full)
 DEPENDS+= +AUDIO_SUPPORT:alsa-lib +libgnutls +libopus \
    +SOFT_FLOAT:shine \
    +!SOFT_FLOAT:lame-lib \
    +libx264 \
	+rkmpp \
	+rkrga \
	+libdrm \
    +fdk-aac
endef

define Package/libffmpeg-mpp/description
$(call Package/ffmpeg/Default/description)
 .
 This package contains full-featured FFmpeg shared libraries.
endef

# Strip off FPU notation
REAL_CPU_TYPE:=$(firstword $(subst +, ,$(CONFIG_CPU_TYPE)))
# Fixup cpu types recogized by ffmpeg configure
REAL_CPU_TYPE:=$(subst octeonplus,octeon+,$(REAL_CPU_TYPE))

FFMPEG_CONFIGURE:= \
	CFLAGS="$(TARGET_CFLAGS) $(TARGET_CPPFLAGS) $(FPIC)" \
	LDFLAGS="$(TARGET_LDFLAGS)" \
	./configure \
	--enable-cross-compile \
	--cross-prefix="$(TARGET_CROSS)" \
	--arch="$(ARCH)" \
	$(if $(REAL_CPU_TYPE),--cpu=$(call qstrip,$(REAL_CPU_TYPE)),) \
	--target-os=linux \
	--prefix="/usr" \
	--pkg-config="pkg-config" \
	--enable-shared \
	--enable-static \
	--enable-pthreads \
	--enable-zlib \
	--disable-doc \
	--disable-debug \
	\
	--disable-lzma \
	--disable-vaapi \
	--disable-vdpau \
	--disable-outdevs

ifeq ($(CONFIG_SOFT_FLOAT),y)
FFMPEG_CONFIGURE+= \
	--disable-altivec \
	--disable-vsx \
	--disable-power8 \
	--disable-armv5te \
	--disable-armv6 \
	--disable-armv6t2 \
	--disable-fast-unaligned \
	--disable-runtime-cpudetect

else ifneq ($(findstring arm,$(CONFIG_ARCH))$(findstring aarch64,$(CONFIG_ARCH)),)
FFMPEG_CONFIGURE+= \
	--disable-runtime-cpudetect
# XXX: GitHub issue 3320 ppc cpu with fpu but no altivec (WNDR4700)
else ifneq ($(findstring powerpc,$(CONFIG_ARCH)),)
FFMPEG_CONFIGURE+= \
	--disable-altivec
endif

# selectively disable optimizations according to arch/cpu type
ifneq ($(findstring arm,$(CONFIG_ARCH)),)
	FFMPEG_CONFIGURE+= --enable-lto

	ifneq ($(findstring vfp,$(CONFIG_CPU_TYPE)),)
		FFMPEG_CONFIGURE+= --enable-vfp
	else
		FFMPEG_CONFIGURE+= --disable-vfp
	endif
	ifneq ($(findstring neon,$(CONFIG_CPU_TYPE)),)
		FFMPEG_CONFIGURE+= \
			--enable-neon \
			--enable-vfp
	else
		FFMPEG_CONFIGURE+= --disable-neon
	endif
endif

ifneq ($(findstring aarch64,$(CONFIG_ARCH)),)
	FFMPEG_CONFIGURE+= \
		--enable-lto \
		--enable-neon \
		--enable-vfp
endif

FFMPEG_DISABLE= \
$(foreach c, $(2), \
	--disable-$(1)="$(c)" \
)

FFMPEG_CONFIGURE+= \
	--enable-gnutls \
	$(if $(CONFIG_BUILD_PATENTED),, \
		$(call FFMPEG_DISABLE,decoder,$(FFMPEG_PATENTED_DECODERS)) \
		$(call FFMPEG_DISABLE,muxer,$(FFMPEG_PATENTED_MUXERS)) \
		$(call FFMPEG_DISABLE,demuxer,$(FFMPEG_PATENTED_DEMUXERS)) \
		$(call FFMPEG_DISABLE,parser,$(FFMPEG_PATENTED_PARSERS))) \
	$(if $(CONFIG_PACKAGE_libopus),--enable-libopus)
ifeq ($(CONFIG_SOFT_FLOAT),y)
FFMPEG_CONFIGURE+= \
	--enable-small \
	\
	$(if $(CONFIG_PACKAGE_shine),--enable-libshine)
else
ifeq ($(ARCH),x86_64)
	FFMPEG_CONFIGURE+= --enable-hardcoded-tables
else
	FFMPEG_CONFIGURE+= --enable-small
endif
FFMPEG_CONFIGURE+= $(if $(CONFIG_PACKAGE_lame-lib),--enable-libmp3lame)
endif

FFMPEG_CONFIGURE+= \
--enable-gpl \
--enable-libx264 \
--enable-version3 \
--enable-nonfree \
--enable-libdrm \
--enable-librga \
--enable-rkmpp \
# --enable-encoder=h264_rkmpp

FFMPEG_CONFIGURE+= $(if $(CONFIG_PACKAGE_fdk-aac),--enable-libfdk-aac)

ifeq ($(CONFIG_AUDIO_SUPPORT),)
FFMPEG_CONFIGURE+= \
	--disable-alsa
endif

ifneq ($(CONFIG_TARGET_x86),)
  TARGET_CFLAGS+= -fomit-frame-pointer
endif

define Build/Prepare
	rm -rf $(PKG_BUILD_DIR)/
	mkdir -p $(PKG_BUILD_DIR)/
	$(TAR) -xzf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR) --strip-components=1
	$(Build/Patch)
endef

define Build/Configure
	( cd $(PKG_BUILD_DIR); $(FFMPEG_CONFIGURE) )
endef

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		all install
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(INSTALL_DIR) $(1)/usr/lib
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/include/lib{avcodec,avdevice,avfilter,avformat,avutil,swresample,swscale} $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/lib{avcodec,avdevice,avfilter,avformat,avutil,swresample,swscale}.{a,so*} $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/lib{avcodec,avdevice,avfilter,avformat,avutil,swresample,swscale}.pc $(1)/usr/lib/pkgconfig/
	$(CP) $(PKG_INSTALL_DIR)/usr/include/libpostproc $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libpostproc.{a,so*} $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/libpostproc.pc $(1)/usr/lib/pkgconfig/
endef

define Package/ffmpeg-mpp/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(PKG_INSTALL_DIR)/usr/bin/ffmpeg $(1)/usr/bin/
endef

define Package/ffprobe-mpp/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(PKG_INSTALL_DIR)/usr/bin/ffprobe $(1)/usr/bin/
endef

define Package/libffmpeg-mpp/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/lib{avcodec,avdevice,avfilter,avformat,avutil,swresample,swscale}.so.* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libpostproc.so.* $(1)/usr/lib/
endef

$(eval $(call BuildPackage,ffmpeg-mpp))
$(eval $(call BuildPackage,ffprobe-mpp))
$(eval $(call BuildPackage,libffmpeg-mpp))
