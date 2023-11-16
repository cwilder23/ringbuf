#
# This file is in the Public Domain.
#
CC:=		@gcc
DESTDIR=	../dist
LIBDIR:=	$(DESTDIR)/lib
INCDIR:=	$(DESTDIR)/include

# libtool is nowlibtoolize. Install on Ubuntu: sudo apt install libtool

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

$(LIB).so:	LDFLAGS+=	-Wl,-rpath=$(LIBDIR)
install/%.so:	ILIBDIR=	$(DESTDIR)/$(LIBDIR)
install:	IINCDIR=	$(DESTDIR)/$(INCDIR)/
#install:	IMANDIR=	$(DESTDIR)/$(MANDIR)/man3/

#all: init obj lib stress tests
all: obj lib tests stress install

#PHONY init:
#	$(info CC=$(CC))	
#	@mkdir -p $(LIBDIR) $(INCDIR)

obj: $(OBJS)

lib: $(LIB).so

%.o: %.c
	$(CC) $(CFLAGS) -c $<

$(LIB).so: $(OBJS)
	$(CC) -shared $(LDFLAGS) $^ -o $@

install: $(LIB).so
	@mkdir -p $(INCDIR) $(LIBDIR)
	@cp $(INCS) $(INCDIR)
	@mv $(LIB).so $(LIBDIR)

tests: $(OBJS) t_ringbuf.o
	$(CC) $(CFLAGS) $^ -o t_ringbuf -lpthread
	./t_ringbuf

stress: $(OBJS) t_stress.o
	$(CC) $(CFLAGS) $^ -o t_stress $(LDFLAGS) -lpthread
	./t_stress

clean:
	@rm -rf *.o *.so t_ringbuf t_stress $(DESTDIR)

.PHONY: all obj lib install tests stress clean
