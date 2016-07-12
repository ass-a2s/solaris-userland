#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#

#
# Copyright (c) 2015, 2016, Oracle and/or its affiliates. All rights reserved.
#

BUILD_BITS =            32_and_64
# All components using gobject-introspection must be built with gcc as
# gobject-introspection does not produce correct results when using Studio.
COMPILER =              gcc
include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		clutter
COMPONENT_VERSION=	1.24.0
COMPONENT_PROJECT_URL=	http://www.clutter-project.org/
COMPONENT_ARCHIVE_HASH=	\
	sha256:85c87d5745b97af7633776419a47421aae7cea66c1b870f88cc5e06a4c5626e6
COMPONENT_BUGDB=        library/clutter

TPNO=			24625

# Requires cogl modules - disable till we get the fixed cogl on build servers
TEST_TARGET = $(SKIP_TEST)

# Needs gobject-introspection, not available on S11.
ifeq ($(BUILD_TYPE), evaluation)
BUILD_32_and_64=
INSTALL_32_and_64=
PUBLISH_STAMP=
endif

include $(WS_MAKE_RULES)/gnome-component.mk

# Uses gcc extensions for void returns.
CFLAGS.studio += $(studio_FEATURES_EXTENSIONS)

CONFIGURE_OPTIONS += --disable-static
# Required for other components.
CONFIGURE_OPTIONS += --enable-introspection
CONFIGURE_OPTIONS += --enable-shared
# Required for other components.
CONFIGURE_OPTIONS += --enable-gdk-backend
# Required for other components.
CONFIGURE_OPTIONS += --enable-gdk-pixbuf
# Required for other components.
CONFIGURE_OPTIONS += --enable-x11-backend
# Required for other components.
CONFIGURE_OPTIONS += --enable-xinput
CONFIGURE_OPTIONS += --with-pic
CONFIGURE_OPTIONS += --with-x

# Delete this when build servers are installed with an updated cogl
COGL_BASE = $(WS_COMPONENTS)/gnome/cogl/build/prototype/$(MACH)
PKG_CONFIG_PATHS.32 += $(COGL_BASE)/usr/lib/pkgconfig/
PKG_CONFIG_PATHS.64 += $(COGL_BASE)/usr/lib/$(MACH64)/pkgconfig/
PKG_CONFIG_PATHS += $(PKG_CONFIG_PATHS.$(BITS))

LD_OPTIONS.32 += -L $(COGL_BASE)/usr/lib 
LD_OPTIONS.64 += -L $(COGL_BASE)/usr/lib/$(MACH64) 
LD_OPTIONS += $(LD_OPTIONS.$(BITS))

CPPFLAGS += -I$(COGL_BASE)/usr/include/cogl

COMPONENT_BUILD_ENV  += \
	XDG_DATA_DIRS="$(COGL_BASE)/usr/share/:/usr/share/" 

COMPONENT_BUILD_ENV += LD_LIBRARY_PATH_32="$(COGL_BASE)$(USRLIB.$(BITS)):$(BUILD_DIR_32)/clutter/.libs"
COMPONENT_BUILD_ENV += LD_LIBRARY_PATH_64="$(COGL_BASE)$(USRLIB.$(BITS)):$(BUILD_DIR_64)/clutter/.libs"

# Work around pkgdepend bug which doesn't resolve dependencies from
# the local workspace repo. - 15708323
DEPENDED += $(WS_TOP)/components/gnome/cogl/build/manifest-$(MACH)-cogl.depend

# Temporary until dependencies are all on build machines
REQUIRED_PACKAGES += $(shell $(GSED) -n 's/REQUIRED_PACKAGES +=//p' ../cogl/Makefile)
#end Delete

REQUIRED_PACKAGES += developer/documentation-tool/gtk-doc
REQUIRED_PACKAGES += library/desktop/atk
REQUIRED_PACKAGES += library/desktop/cairo
REQUIRED_PACKAGES += library/desktop/gdk-pixbuf
REQUIRED_PACKAGES += library/desktop/gobject/gobject-introspection
REQUIRED_PACKAGES += library/desktop/gtk3
REQUIRED_PACKAGES += library/desktop/pango
REQUIRED_PACKAGES += library/glib2
REQUIRED_PACKAGES += library/graphics/cogl
REQUIRED_PACKAGES += library/json-glib
REQUIRED_PACKAGES += system/library/fontconfig
REQUIRED_PACKAGES += system/library/gcc/gcc-c-runtime
REQUIRED_PACKAGES += system/library/math
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxcomposite
REQUIRED_PACKAGES += x11/library/libxdamage
REQUIRED_PACKAGES += x11/library/libxi