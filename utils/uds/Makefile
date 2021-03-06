#
# Copyright (c) 2017 Red Hat, Inc.
#
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License
# as published by the Free Software Foundation; either version 2
# of the License, or (at your option) any later version.
# 
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
# 
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA
# 02110-1301, USA. 
#
# $Id: //eng/vdo-releases/magnesium/src/packaging/src-dist/user/utils/uds/Makefile#1 $

# UDS_INTERNAL_VERSION is the short version name, which is used to version the
# UDS interfaces exported from libuds.so.  DISTRIBUTION_VERSION is the long
# version name used in distribution builds.  We extract these values from the
# traditional location.
UDS_VERSION      = 6.1.0.19
BUILD_VERSION    = $(UDS_VERSION)
DISTRO_CODENAME := $(shell lsb_release -s -c)

DEPDIR  = .deps

DOXY_DIR  = $(TOP_DIR)/src/doxygen
DOXY_FILE = $(TOP_DIR)/src/public/Doxyfile.in
DOXY_EDIT = sed -e 's/@MARKETING_VERSION@/$(BUILD_VERSION)/'
DOXY_WARN_LOG = $(shell grep '^WARN_LOGFILE' $(DOXY_FILE) | cut -f 2 -d=)

ifeq ($(origin CC), default)
  CC=gcc
endif

WARNS =	-Wall			\
	-Wcast-align		\
	-Werror			\
	-Wextra			\
	-Winit-self		\
	-Wlogical-op		\
	-Wmissing-include-dirs	\
	-Wpointer-arith		\
	-Wredundant-decls	\
	-Wunused		\
	-Wwrite-strings

C_WARNS =	-Wbad-function-cast		\
		-Wcast-qual			\
		-Wfloat-equal			\
		-Wformat=2			\
		-Wmissing-declarations		\
		-Wmissing-format-attribute	\
		-Wmissing-prototypes		\
		-Wnested-externs		\
		-Wold-style-definition		\
		-Wswitch-default

OPT_FLAGS     = -O3 -fno-omit-frame-pointer
DEBUG_FLAGS   =
GLOBAL_FLAGS  = -D_GNU_SOURCE -g $(OPT_FLAGS) $(WARNS)		\
		$(shell getconf LFS_CFLAGS) $(DEBUG_FLAGS)	\
		-DUDS_VERSION='"$(BUILD_VERSION)"'		\

CFLAGS  = $(GLOBAL_FLAGS) -I. -std=c99 $(C_WARNS) -fpic -pedantic $(MY_CFLAGS)
LDFLAGS = $(MY_LDFLAGS)

MY_FLAGS    =
MY_CFLAGS   = $(MY_FLAGS)
MY_LDFLAGS  =

IMPORTED_SYMBOLS = libuds.imported

vpath %.c .
vpath %.c ./murmur
vpath %.c ./util

UDS_OBJECTS =	MurmurHash3.o			\
		buffer.o			\
		directoryReader.o		\
		errors.o			\
		fileUtils.o			\
		logger.o			\
		loggerLinuxUser.o		\
		memoryAlloc.o			\
		memoryLinuxUser.o		\
		minisyslog.o			\
		numeric.o			\
		permassert.o			\
		permassertLinuxUser.o		\
		stringUtils.o			\
		syscalls.o			\
		threadsLinuxUser.o		\
		threadMutexLinuxUser.o		\
		threadOnce.o			\
		timeUtils.o			\
		timeUtilsLinuxUser.o

.PHONY: all
all: libuds.a

.PHONY: clean
clean:
	rm -f *.o *.a *.s *.so lib*.so.* wrapped-symbols
	rm -rf doxygen $(DEPDIR)

.PHONY: install
install:;

# Suppress a pedantic warning, which we can't fix without some serious
# hacking and serious divergence from upstream:
#
# sha256.c:750: error: string length '38605' is greater than the length '4095' ISO C99 compilers are required to support
#
# The string in question is one massive asm() statement; we can't
# split that up without adding lots of extra constraints to force the
# compiler not to move things around in between them.  (Even "asm
# volatile" doesn't guarantee that it won't.)
#
# Alternatively, we could yank out the asm code and stick it in a .s
# or .S file, but that makes updating from upstream even harder.
sha256.o: CFLAGS:= $(filter-out -pedantic,$(CFLAGS)) # -save-temps

libuds.a: $(UDS_OBJECTS)
	rm -f $@
	ar cr $@ $^

%.s: %.c
	$(CC) $(CFLAGS) -S $^


########################################################################
# Dependency processing

%.o: %.c
	@mkdir -p $(DEPDIR)/$(@D) $(@D)
	$(COMPILE.c) -MD -MF $(DEPDIR)/$*.d.new -MP -MT $@ $< -o $@
	if cmp -s $(DEPDIR)/$*.d $(DEPDIR)/$*.d.new; then \
		rm -f $(DEPDIR)/$*.d.new ; \
	else \
		mv -f $(DEPDIR)/$*.d.new $(DEPDIR)/$*.d ; \
	fi

$(DEPDIR)/%.d: %.c
	@mkdir -p $(@D)
	$(CC) $(CFLAGS) -MM -MF $@ -MP -MT $*.o $<

ifneq ($(MAKECMDGOALS),clean)
-include $(UDS_OBJECTS:%.o=$(DEPDIR)/%.d)
endif
