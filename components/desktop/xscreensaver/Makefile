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
# Copyright (c) 2016, Oracle and/or its affiliates. All rights reserved.
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		xscreensaver
COMPONENT_VERSION=	5.15
COMPONENT_PROJECT_URL=	https://www.jwz.org/xscreensaver/
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.gz
COMPONENT_ARCHIVE_HASH=	\
    sha256:4f6d1f1e4c15dbb74e2296f8fe57a73d47d602515178c248bbc838f779d5082d
#COMPONENT_ARCHIVE_URL=	https://www.jwz.org/xscreensaver/$(COMPONENT_ARCHIVE)
# jwz removes older tarballs, and 5.15 is no longer on his site, so borrow
# Fedora's archive until we update
COMPONENT_ARCHIVE_URL=	http://pkgs.fedoraproject.org/repo/pkgs/xscreensaver/xscreensaver-5.15.tar.gz/c0b8b2c817a9a7371f51a82e80602d10/$(COMPONENT_ARCHIVE)
COMPONENT_BUGDB=        gnome/screensaver

TPNO=			9370

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/configure-64.mk
include $(WS_MAKE_RULES)/ips.mk

# While the tree currently builds with Studio, we need to make sure it also
# builds with gcc when submitting patches upstream, and having both sets of
# options in the CFLAGS below makes it easy to switch when needed.
# COMPILER=gcc
CC += $(CC_BITS)

# Enable additional warnings, since we've modified this code a lot
CFLAGS.studio += -v
CFLAGS.gcc    += -Wall

# Additional optimization flags, to make the hacks show off the hardware
# better and because for just a screensaver display we can get away with
# using optimizations that may change strict correctness of floating point ops.
CFLAGS.studio += -fsimple=2 -nofstore -xlibmil -xprefetch
CFLAGS.gcc    += -funsafe-math-optimizations

# Regenerate configure after patches modify configure.in
COMPONENT_PREP_ACTION += ( cd $(@D); autoreconf -fiv);

# Need to pick up the GNU version of xgettext.
PATH=$(GNUBIN):/usr/bin

# Libraries & programs to depend on
CONFIGURE_OPTIONS += --with-gl=$(USRDIR)
CONFIGURE_OPTIONS += --with-gtk=$(USRDIR)
CONFIGURE_OPTIONS += --with-jpeg=$(USRDIR)
CONFIGURE_OPTIONS += --without-motif
CONFIGURE_OPTIONS += --with-pixbuf=$(USRDIR)
CONFIGURE_OPTIONS += --with-xft=$(USRDIR)
CONFIGURE_OPTIONS += PTHREAD_CFLAGS="-D_POSIX_PTHREAD_SEMANTICS"
CONFIGURE_OPTIONS += PTHREAD_LIBS="-lc"
CONFIGURE_OPTIONS += PERL=$(PERL)
CONFIGURE_OPTIONS += INTLTOOL_PERL=$(PERL)

# X extensions to support
CONFIGURE_OPTIONS += --with-dpms-ext
CONFIGURE_OPTIONS += --with-randr-ext
CONFIGURE_OPTIONS += --with-xinerama-ext
CONFIGURE_OPTIONS += --with-xinput-ext  # required for accessibility

# Support locking the screen by normal users and by root
CONFIGURE_OPTIONS += --enable-locking
CONFIGURE_OPTIONS += --enable-root-passwd
# and allow user switching from the lock screen with gdmflexiserver
CONFIGURE_OPTIONS += --with-login-manager

# Use only PAM, not direct /etc/shadow or kerberos access
CONFIGURE_OPTIONS += --with-pam=$(USRDIR)
CONFIGURE_OPTIONS += --without-shadow
CONFIGURE_OPTIONS += --without-kerberos

# Paths to install to
APP_DEFAULTS_DIR  = $(CONFIGURE_PREFIX)/share/X11/app-defaults
XSS_LIBDIR 	  = $(CONFIGURE_PREFIX)/lib/xscreensaver
CONFIGURE_OPTIONS += --bindir=$(CONFIGURE_BINDIR.32)
CONFIGURE_OPTIONS += --libexecdir=$(XSS_LIBDIR)/bin
CONFIGURE_OPTIONS += --with-hackdir=$(XSS_LIBDIR)/hacks
CONFIGURE_OPTIONS += --with-configdir=$(XSS_LIBDIR)/config/control-center-2.0
CONFIGURE_OPTIONS += --with-x-app-defaults=$(APP_DEFAULTS_DIR)

# Data sources for images & text in hacks
CONFIGURE_OPTIONS += --with-image-directory=$(USRSHAREDIR)/pixmaps/backgrounds
CONFIGURE_OPTIONS += --with-text-file=/etc/motd

# Do not install setuid hacks
CONFIGURE_OPTIONS += --without-setuid-hacks

# The potfiles.in updates don't work unless source is cloned to build dir
COMPONENT_PRE_CONFIGURE_ACTION += ($(CLONEY) $(SOURCE_DIR) $(@D));

# Update potfiles.in to pick up our added sources like lock-Gtk.c
# The "-o Makefile" & touch stamp-it is needed to avoid infinite looping.
COMPONENT_POST_CONFIGURE_ACTION += ( \
    cd $(@D)/po ; \
    $(MAKE) -o Makefile generate_potfiles_in ; \
    $(MAKE) -o Makefile POTFILES ; \
    $(MAKE) -o Makefile xscreensaver.pot ; \
    touch stamp-it )

# Recognizing DESTDIR as most autotools packages do would be too easy,
# so xscreensaver makes us override install_prefix instead for the same effect.
COMPONENT_INSTALL_ARGS += install_prefix=$(PROTO_DIR)

# Remove hacks we've chosen not to ship at all for various reasons
REMOVED_HACKS="extrusion flyingtoasters ljlatest sonar webcollage"
COMPONENT_POST_INSTALL_ACTION += ( \
for h in $(REMOVED_HACKS) ; do \
    rm -f $(PROTO_DIR)$(XSS_LIBDIR)/hacks/$${h} \
          $(PROTO_DIR)$(CONFIGURE_MANDIR)/man6/$${h}.6 \
          $(PROTO_DIR)$(XSS_LIBDIR)/config/control-center-2.0/$${h}.xml ;\
done ; \
rm -f $(PROTO_DIR)$(XSS_LIBDIR)/hacks/webcollage-helper );


ASLR_MODE = $(ASLR_ENABLE)

configure:      $(CONFIGURE_64)
build:          $(BUILD_64)
install:        $(INSTALL_64)
test:           $(NO_TESTS)
system-test:    $(NO_TESTS)


REQUIRED_PACKAGES += gnome/accessibility/gnome-a11y-libs
REQUIRED_PACKAGES += gnome/config/gconf
REQUIRED_PACKAGES += image/library/libjpeg
REQUIRED_PACKAGES += library/desktop/atk
REQUIRED_PACKAGES += library/desktop/gdk-pixbuf
REQUIRED_PACKAGES += library/desktop/gtk2
REQUIRED_PACKAGES += library/desktop/libglade
REQUIRED_PACKAGES += library/desktop/pango
REQUIRED_PACKAGES += library/glib2
REQUIRED_PACKAGES += library/gnome/gnome-component
REQUIRED_PACKAGES += library/libxml2
REQUIRED_PACKAGES += library/perl-5/xml-parser
REQUIRED_PACKAGES += $(PERL_PKG)
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/math
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxext
REQUIRED_PACKAGES += x11/library/libxft
REQUIRED_PACKAGES += x11/library/libxi
REQUIRED_PACKAGES += x11/library/libxinerama
REQUIRED_PACKAGES += x11/library/libxmu
REQUIRED_PACKAGES += x11/library/libxrandr
REQUIRED_PACKAGES += x11/library/libxxf86misc
REQUIRED_PACKAGES += x11/library/libxxf86vm
REQUIRED_PACKAGES += x11/library/mesa
REQUIRED_PACKAGES += x11/library/toolkit/libxt