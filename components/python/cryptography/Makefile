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
# Copyright (c) 2014, 2019, Oracle and/or its affiliates. All rights reserved.
#

COMPONENT_NAME=		cryptography
COMPONENT_VERSION=	2.5
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.gz
COMPONENT_ARCHIVE_HASH=	\
    sha256:4946b67235b9d2ea7d31307be9d5ad5959d6c4a8f98f900157b47abddf698401
COMPONENT_ARCHIVE_URL=	$(call pypi_url)
COMPONENT_PROJECT_URL=	https://cryptography.io/
COMPONENT_BUGDB=	python-mod/cryptography
COMPONENT_ANITYA_ID=	5532

TPNO=			49366

COMPILER=		gcc

# Component uses ABI3 naming.
PYTHON3_SOABI=abi3

include ../../../make-rules/shared-macros.mk

# Temporary solution for Python3.7 ready components
PYTHON_VERSIONS = $(PYTHON3.7_READY)

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/setup.py.mk
include $(WS_MAKE_RULES)/ips.mk

ASLR_MODE = $(ASLR_NOT_APPLICABLE)

# Use the CFFI version in the gate because if the CFFI version changes
# everything using CFFI will break.
CFFI = $(WS_COMPONENTS)/python/cffi/build/prototype/$(MACH)/$(PYTHON_LIB)

COMPONENT_BUILD_ENV +=		PYTHONPATH=$(CFFI)
COMPONENT_INSTALL_ENV +=	PYTHONPATH=$(CFFI)

# common targets
build:		$(BUILD_32_and_64)

install:	$(INSTALL_32_and_64)

#
# tests require cryptography_vectors, iso8601, pretend, pytest>= 2.8.7,
# hypothesis>=1.11.4 and pyasn1_modules, some of which have not yet integrated.
# On a i386 kernel zone with the test dependencies installed, test results are:
# Python2.7: 96918 passed, 8972 skipped
# Python3.4: 96918 passed, 8972 skipped
# Python3.5: 96918 passed, 8972 skipped
# Python3.7: 96914 passed, 8976 skipped
#
test:		$(NO_TESTS)

system-test:    $(SYSTEM_TESTS_NOT_IMPLEMENTED)

REQUIRED_PACKAGES += library/python/cffi
REQUIRED_PACKAGES += library/security/openssl
REQUIRED_PACKAGES += system/library
