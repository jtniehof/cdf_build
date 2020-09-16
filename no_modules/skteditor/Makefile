PREFIX ?= /usr/local
prefix ?= $(PREFIX)#preferred
LIBDIR ?= $(prefix)/lib
libdir ?= $(LIBDIR)#preferred
BINDIR ?= $(prefix)/bin
bindir ?= $(BINDIR)
INSTALL ?= /usr/bin/install
INSTALL_DATA ?= $(INSTALL) -m 644
MV ?= mv
TAR ?= tar
UNZIP ?= unzip
SED ?= sed
CP ?= cp
LN_S ?= ln -s
WGET ?= wget

include versions.mk
#Where the unpacked original download lives
DISTDIR = skteditor-$(VER)

MANIFEST = Makefile README.txt $(INZIP) versions.mk istpcheck.sh.in skteditor.sh.in
JARS = 

all: build

build: istpcheck.sh skteditor.sh $(DISTDIR)

download: $(INZIP)

$(INZIP):
	$(WGET) https://spdf.gsfc.nasa.gov/skteditor/$@

distdir: $(DISTDIR)

$(DISTDIR): | $(INZIP)
	$(UNZIP) $(INZIP)

%.sh: %.sh.in
	$(SED) -e "s|@LIBDIR@|$(libdir)|g" $< > $@

install: skteditor.sh $(DISTDIR)/*.jar $(DISTDIR)/extensions/*.jar | $(DISTDIR)
	$(INSTALL) -d $(DESTDIR)$(libdir)/skteditor/extensions/
	$(INSTALL_DATA) -t $(DESTDIR)$(libdir)/skteditor $(DISTDIR)/*.jar
	$(INSTALL_DATA) -t $(DESTDIR)$(libdir)/skteditor/extensions $(DISTDIR)/extensions/*.jar
	$(INSTALL) -d $(DESTDIR)$(bindir)
	$(INSTALL) -t $(DESTDIR)$(bindir) istpcheck.sh skteditor.sh

uninstall:
	rm -rf $(DESTDIR)$(libdir)/skteditor
	rm -f $(DESTDIR)$(bindir)/skteditor.sh
	rm -f $(DESTDIR)$(bindir)/istpcheck.sh

skteditor-$(VER).tar.gz: $(MANIFEST)
	rm -f $@
	$(TAR) zcf $@ $(MANIFEST) -h --transform "s,^,skteditor-$(VER)/,"

dist: skteditor-$(VER).tar.gz

clean:
	-rm -rf *~ skteditor-$(VER).tar.gz istpcheck.sh skteditor.sh $(DISTDIR)

distclean: clean
	-rm -f $(INZIP)

.PHONY: build clean dist distclean download install uninstall
