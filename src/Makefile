#
# This file is in the Public Domain.
#

DESTDIR=	../dist
LIBDIR:=	$(DESTDIR)/lib
INCDIR:=	$(DESTDIR)/include

# libtool is now libtoolize. Install on Ubuntu: sudo apt install libtool
LIBTOOL=	/usr/bin/libtoolize

SYSNAME:=	$(shell uname -s)
SYSARCH:=	$(shell uname -m)

CFLAGS=		-std=c11 -O2 -g -W -Wextra -Werror
CFLAGS+=	-D_POSIX_C_SOURCE=200809L
CFLAGS+=	-D_GNU_SOURCE -D_DEFAULT_SOURCE

#
# Extended warning flags.
#
CFLAGS+=	-Wno-unknown-warning-option # gcc vs clang

CFLAGS+=	-Wstrict-prototypes -Wmissing-prototypes -Wpointer-arith
CFLAGS+=	-Wmissing-declarations -Wredundant-decls -Wnested-externs
CFLAGS+=	-Wshadow -Wcast-qual -Wcast-align -Wwrite-strings
CFLAGS+=	-Wold-style-definition
CFLAGS+=	-Wsuggest-attribute=noreturn -Wjump-misses-init
CFLAGS+=	-Wduplicated-cond -Wmisleading-indentation -Wnull-dereference
CFLAGS+=	-Wduplicated-branches -Wrestrict

ifeq ($(MAKECMDGOALS),tests)
DEBUG=		1
endif

ifeq ($(MAKECMDGOALS),stress)
DEBUG=		1
endif

ifeq ($(DEBUG),1)
CFLAGS+=	-Og -DDEBUG -fno-omit-frame-pointer
ifeq ($(SYSARCH),x86_64)
CFLAGS+=	-fsanitize=address -fsanitize=undefined
LDFLAGS+=	-fsanitize=address -fsanitize=undefined
endif
else
CFLAGS+=	-DNDEBUG
endif

LIB=		libringbuf
INCS=		ringbuf.h

OBJS=		ringbuf.o

$(LIB).la:	LDFLAGS+=	-rpath $(LIBDIR)
install/%.la:	ILIBDIR=	$(DESTDIR)/$(LIBDIR)
install:	IINCDIR=	$(DESTDIR)/$(INCDIR)/
#install:	IMANDIR=	$(DESTDIR)/$(MANDIR)/man3/

all: init obj lib

PHONY init: 
	@mkdir -p $(LIBDIR) $(INCDIR)

obj: $(OBJS)

lib: $(LIB).la

%.lo: %.c
	$(LIBTOOL) --mode=compile --tag CC $(CC) $(CFLAGS) -c $<

$(LIB).la: $(shell echo $(OBJS) | sed 's/\.o/\.lo/g')
	$(LIBTOOL) --mode=link --tag CC $(CC) $(LDFLAGS) -o $@ $(notdir $^)

install/%.la: %.la
	mkdir -p $(ILIBDIR)
	$(LIBTOOL) --mode=install install -c $(notdir $@) $(ILIBDIR)/$(notdir $@)

install: $(addprefix install/,$(LIB).la)
	$(LIBTOOL) --mode=finish $(LIBDIR)
	mkdir -p $(IINCDIR) && install -c $(INCS) $(IINCDIR)
	#mkdir -p $(IMANDIR) && install -c $(MANS) $(IMANDIR)

tests: $(OBJS) t_ringbuf.o
	$(CC) $(CFLAGS) $^ -o t_ringbuf -lpthread
	./t_ringbuf

stress: $(OBJS) t_stress.o
	$(CC) $(CFLAGS) $^ -o t_stress $(LDFLAGS) -lpthread
	./t_stress

clean:
	$(LIBTOOL) --mode=clean rm
	rm -rf .libs *.o *.lo *.la t_ringbuf t_stress $(DISTDIR)

.PHONY: all obj lib install tests stress clean
