include ../../../mk/pitchfork.mk

# CXX check
include ../../../mk/chkcxx.mk

# Local variables
_NAME            = unanimity
$(_NAME)_REPO   ?= git://github.com/PacificBiosciences/$(_NAME)
_WRKSRC          = $(WORKDIR)/$(_NAME)
$(_NAME)_VERSION?= HEAD

# Local works
do-fetch: $(_WRKSRC)
$(_WRKSRC):
ifeq ($(wildcard $($(_NAME)_REPO)),)
	$(GIT) clone $($(_NAME)_REPO) $@
	cd $(_WRKSRC) && $(GIT) checkout $($(_NAME)_VERSION)
DEVOPT =
else
	ln -sfn $($(_NAME)_REPO) $(_WRKSRC)
#DEVOPT = -e
endif
do-config: $(_WRKSRC)/_build/Makefile
$(_WRKSRC)/_build/Makefile: | do-fetch
	mkdir -p $(_WRKSRC)/_build
	cd $(_WRKSRC)/_build \
     && $(CMAKE) \
          -DUNY_build_tests=OFF \
          -DZLIB_INCLUDE_DIRS=$(PREFIX)/include \
          -DZLIB_LIBRARIES=$(PREFIX)/lib/libz.so \
          -DBoost_INCLUDE_DIRS=$(BOOST_ROOT)/include \
          -DPacBioBAM_INCLUDE_DIRS=$(PREFIX)/include \
          -DPacBioBAM_LIBRARIES=$(PREFIX)/lib/libpbbam.$(DYLIB) \
          -DHTSLIB_INCLUDE_DIRS=$(PREFIX)/src/htslib/include \
          -DHTSLIB_LIBRARIES=$(PREFIX)/src/htslib/lib/libhts.a \
          -DSEQAN_INCLUDE_DIRS=$(PREFIX)/include \
        ..
do-build:
	cd $(_WRKSRC)/_build && $(MAKE)
do-install: $(PREFIX)/var/pkg/$(_NAME)
$(PREFIX)/var/pkg/$(_NAME): | do-build
	rsync -ax $(_WRKSRC)/_build/ccs $(PREFIX)/bin/
	@echo bin/ccs > $@ 
	    CMAKE_COMMAND=$(CMAKE) \
        Boost_INCLUDE_DIRS=$(BOOST_ROOT)/include \
              SWIG_COMMAND=$(shell . $(PREFIX)/setup-env.sh && which swig) \
                   VERBOSE=1 \
        $(PIP) install --verbose --upgrade --no-deps $(DEVOPT) $(_WRKSRC)/
	@echo pip uninstall $(_NAME) >> $@
do-clean:
	$(MAKE) -C $(_WRKSRC)/_build clean
do-distclean:
	cd $(_WRKSRC) && $(GIT) clean -xdf
do-flush:
	rm -rf $(_WRKSRC)