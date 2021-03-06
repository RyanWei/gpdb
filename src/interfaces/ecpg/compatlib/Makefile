#-------------------------------------------------------------------------
#
# Makefile for ecpg compatibility library
#
# Portions Copyright (c) 1996-2014, PostgreSQL Global Development Group
# Portions Copyright (c) 1994, Regents of the University of California
#
# src/interfaces/ecpg/compatlib/Makefile
#
#-------------------------------------------------------------------------

subdir = src/interfaces/ecpg/compatlib
top_builddir = ../../../..
include $(top_builddir)/src/Makefile.global

# The frontend doesn't need everything that's in LIBS, some are backend only
LIBS := $(filter-out -lresolv -lbz2, $(LIBS))
# This program isn't interactive, so doesn't need these
LIBS := $(filter-out -lreadline -ledit -ltermcap -lncurses -lcurses -lcurl -lssl -lcrypto -lz, $(LIBS))

NAME= ecpg_compat
SO_MAJOR_VERSION= 3
SO_MINOR_VERSION= 6

override CPPFLAGS := -I../include -I$(top_srcdir)/src/interfaces/ecpg/include \
	-I$(libpq_srcdir) -I$(top_srcdir)/src/include/utils -DFRONTEND $(CPPFLAGS)
override CFLAGS += $(PTHREAD_CFLAGS)

SHLIB_LINK_INTERNAL = -L../ecpglib -lecpg -L../pgtypeslib -lpgtypes $(libpq)
SHLIB_LINK = $(filter -lintl -lm, $(LIBS)) $(PTHREAD_LIBS)
SHLIB_PREREQS = submake-ecpglib submake-pgtypeslib

# For a bug in gcc on centos 32-bit
ifeq ($(GCC), yes)
 CFLAGS += -Wno-error
endif # GCC

SHLIB_EXPORTS = exports.txt

SHLIB_EXPORTS = exports.txt

# Need to recompile any libpgport object files
LIBS := $(filter-out -lpgport, $(LIBS))

OBJS= informix.o $(filter snprintf.o, $(LIBOBJS))

PKG_CONFIG_REQUIRES_PRIVATE = libecpg libpgtypes

all: all-lib

.PHONY: submake-ecpglib submake-pgtypeslib

submake-ecpglib:
	$(MAKE) -C $(top_builddir)/src/interfaces/ecpg/ecpglib all

submake-pgtypeslib:
	$(MAKE) -C $(top_builddir)/src/interfaces/ecpg/pgtypeslib all

# Shared library stuff
include $(top_srcdir)/src/Makefile.shlib

snprintf.c: % : $(top_srcdir)/src/port/%
	rm -f $@ && $(LN_S) $< .

install: all installdirs install-lib

installdirs: installdirs-lib

uninstall: uninstall-lib

clean distclean: clean-lib
	rm -f $(OBJS) snprintf.c

maintainer-clean: distclean maintainer-clean-lib
