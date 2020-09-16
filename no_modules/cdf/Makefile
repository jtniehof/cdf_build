PREFIX ?= /usr/local
prefix ?= $(PREFIX)#preferred
LIBDIR ?= $(prefix)/lib
libdir ?= $(LIBDIR)#preferred
DATAROOTDIR ?= $(prefix)/share
datarootdir ?= $(DATAROOTDIR)#this capitalization is preferred
DATADIR ?= $(datarootdir)
datadir ?= $(DATADIR)
MODULESDIR ?= $(datadir)/modules/modulefiles
modulesdir ?= $(MODULESDIR)
BINDIR ?= $(prefix)/bin
bindir ?= $(BINDIR)
INCLUDEDIR ?= $(prefix)/include
includedir ?= $(INCLUDEDIR)
JAVA_HOME ?= /usr/lib/jvm/default-java

GCC ?= gcc
INSTALL ?= /usr/bin/install
INSTALL_DATA ?= $(INSTALL) -m 644
LD ?= ld
MV ?= mv
TAR ?= /bin/tar
SED ?= sed
CP ?= cp
LN_S ?= ln -s
WGET ?= wget
ECHO ?= echo

CD = $(shell pwd)
TARGET = $(CD)/target

include versions.mk
SHORTVER ?= $(shell echo $(VER) | cut -d . -f 1-2)
BASEURL = https://cdaweb.gsfc.nasa.gov/pub/software/cdf/dist/
DLDIR_BASE = cdf$(subst .,,$(SHORTVER))_$(shell echo $(VER) | cut -d . -f 3)
SUBSUBMINOR = $(shell echo $(VER) | cut -d . -f 4)
ifeq ($(SUBSUBMINOR),)
DLDIR = $(DLDIR_BASE)
else
DLDIR = $(DLDIR_BASE)_$(SUBSUBMINOR)
endif

DISTDIR = $(DLDIR_BASE)-dist
DEFINITIONSFILES = definitions.K definitions.B definitions.C
DEFINITIONSPATHS = $(foreach f,$(DEFINITIONSFILES),$(DISTDIR)/src/definitions/$(f))
DEFINITIONSIN = $(foreach f,$(DEFINITIONSFILES),$(DISTDIR)/src/definitions/$(f).in)
BUILTLIB = $(DISTDIR)/src/lib/libcdf.so
JNILIB = $(DISTDIR)/cdfjava/jni/libcdfNativeLibrary.so

MANIFEST = Makefile README.txt $(INTAR) cdf.in versions.mk idl_cdf.so idl_cdf.dlm cdftools.sh.in

all: build

build: $(SHORTVER) $(DISTDIR) $(BUILTLIB) $(JNILIB) $(DEFINITIONSPATHS) idl_cdf.so idl_cdf.dlm cdftools.sh

download: $(INTAR) idl_cdf.so idl_cdf.dlm

$(DEFINITIONSIN): $(DISTDIR)

$(DEFINITIONSPATHS): %: %.in
	$(SED) -e "s,<cdf_install_dir>,$(prefix)," $< > $@
# The CDF makefiles die if we don't handle this for them, but they
# don't necessarily do it right, so leave a fake one hanging around
# so the search-and-replace doesn't return an error.
	$(ECHO) "# <cdf_install_dir>" >> $@

$(BUILTLIB): $(DISTDIR)
	$(MAKE) OS=linux ENV=gnu SHARED=yes CURSES=yes FORTRAN=no UCOPTIONS=-O2 all -j -l6 -C $(DISTDIR)

$(JNILIB): $(BUILTLIB)
	$(GCC) -I$(DISTDIR)/src/include -fPIC -I$(JAVA_HOME)/include -I$(JAVA_HOME)/include/linux -c $(DISTDIR)/cdfjava/jni/cdfNativeLibrary.c -o $(DISTDIR)/cdfjava/jni/cdfNativeLibrary.o
	$(LD) -shared $(DISTDIR)/cdfjava/jni/cdfNativeLibrary.o -o $@ -L$(DISTDIR)/src/lib -lc -lm -lcdf

$(SHORTVER): cdf.in
	$(SED) -e "s/@CDFVER@/$(SHORTVER)/" -e "s,@CDFBASE@,$(prefix)," -e "s,@CDFLIB@,$(libdir)," -e "s,@CDFBIN@,$(bindir)," -e "s,@CDFINC@,$(includedir)," -e "s,@DATADIR@,$(datadir)," $< > $@

cdftools.sh: cdftools.sh.in
	$(SED) -e "s,@CDFBASE@,$(prefix)," -e "s,@CDFLIB@,$(libdir),g" $< > $@

$(INTAR):
	$(WGET) $(BASEURL)$(DLDIR)/unix/$@

idl_cdf.so:
	$(WGET) $(BASEURL)$(DLDIR)/idl/linux/linux64/$@

idl_cdf.dlm:
	$(WGET) $(BASEURL)$(DLDIR)/idl/$@

distdir: $(DISTDIR)

$(DISTDIR): $(INTAR)
	-rm -rf $(DISTDIR)
	$(TAR) -xf $(INTAR)
	$(foreach f,$(DEFINITIONSFILES),$(MV) $(DISTDIR)/src/definitions/$(f) $(DISTDIR)/src/definitions/$(f).in;)

install: $(SHORTVER) $(DEFINITIONSPATHS) $(JNILIB) $(BUILTLIB) idl_cdf.so idl_cdf.dlm cdftools.sh
	$(MAKE) INSTALLDIR=$(DESTDIR)$(prefix) -C $(DISTDIR) install
	$(INSTALL) -d $(DESTDIR)$(modulesdir)/cdf
	$(INSTALL_DATA) -t $(DESTDIR)$(modulesdir)/cdf $(SHORTVER)
	$(MV) $(DESTDIR)$(prefix)/CDFLeapSeconds.txt $(DESTDIR)$(datadir)
	$(INSTALL) $(JNILIB) $(DESTDIR)$(libdir)
	$(CP) -r $(DISTDIR)/cdfjava $(DESTDIR)$(libdir)/
	$(INSTALL) -d $(DESTDIR)$(libdir)/idl_dlm
	$(INSTALL) -t $(DESTDIR)$(libdir)/idl_dlm idl_cdf.so idl_cdf.dlm
	$(INSTALL) cdftools.sh $(DESTDIR)$(bindir)/
	-echo "You just installed a shared library. Might want to run ldconfig."

cdf-$(VER).tar.gz: $(MANIFEST)
	rm -f $@
	$(TAR) zcf $@ $(MANIFEST) -h --transform "s,^,cdf-$(VER)/,"

dist: cdf-$(VER).tar.gz

clean:
	-$(MAKE) -C $(DISTDIR) clean
	-rm -rf *~ $(SHORTVER) cdf-$(VER).tar.gz cdftools.sh

distclean: clean
	-rm -rf $(DISTDIR)
	-rm -f $(INTAR) idl_cdf.dlm idl_cdf.so

.PHONY: clean dist distclean distdir install
