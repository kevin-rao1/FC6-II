#
# AltOS build
#

vpath make-altitude util
vpath make-altitude-pa util
vpath make-kalman util
vpath make-whiten util
vpath kalman.5c kalman
vpath kalman_filter.5c kalman
vpath kalman_micro.5c kalman
vpath load_csv.5c kalman
vpath matrix.5c kalman

TOPDIR=.

include Makedefs

# STM32F1 (Cortex-M3) targets retained for the competition fork.
ARMM3DIRS=\
	easymega-v3.0 easymega-v3.0/flash-loader \
	stm32f103-nucleo

ARMM0DIRS=
AVRDIRS=

SUBDIRS=draw

ifeq ($(strip $(HAVE_ARM_M3_CC)),yes)
SUBDIRS+=$(ARMM3DIRS)
endif

ALLDIRS=$(ARMM3DIRS) $(ARMM0DIRS) $(AVRDIRS)

all: all-local all-recursive

RECURSIVE_TARGETS = all-recursive install-recursive

$(RECURSIVE_TARGETS):
	@target=`echo $@ | sed 's/-recursive//'`; \
	for subdir in $(SUBDIRS); do \
		echo "Making $$target in $$subdir"; \
		(cd $$subdir && $(MAKE) $$target) || exit 1; \
	done

ALL_RECURSIVE_TARGETS = clean-recursive

$(ALL_RECURSIVE_TARGETS):
	@target=`echo $@ | sed 's/-recursive//'`; \
	for subdir in $(ALLDIRS); do \
		echo "Making $$target in $$subdir"; \
		(cd $$subdir && $(MAKE) $$target) || exit 1; \
	done

distclean:	clean

clean: clean-local clean-recursive

install: install-recursive

uninstall:

all-recursive: all-local

all-local: altitude.h altitude-pa.h altitude-pa-small.h ao_kalman.h ao_whiten.h $(PDCLIB)

altitude.h: make-altitude
	nickle $< > $@

altitude-pa.h: make-altitude-pa
	nickle $< > $@

altitude-pa-small.h: make-altitude-pa
	nickle $< --sample 3 > $@

ao_kalman.h: make-kalman kalman.5c kalman_micro.5c kalman_filter.5c load_csv.5c matrix.5c
	bash $< kalman > $@

ao_whiten.h: make-whiten
	nickle $< > $@

clean-local: $(CLEAN_PDCLIB)
	rm -f altitude.h ao_kalman.h
