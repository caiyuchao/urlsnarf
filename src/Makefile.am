
# what flags you want to pass to the C compiler & linker
#AM_CFLAGS = -I. -I../missing 
AM_LDFLAGS = -L./missing -lmissing

# this lists the binaries to produce, the (non-PHONY, binary) targets in
# the previous manual Makefile
sbin_PROGRAMS = urlsnarf 
urlsnarf_SOURCES = urlsnarf.c base64.c buf.c pcaputil.c
