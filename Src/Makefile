# Mixmaster version 3.0  --  (C) 1999 - 2006 Anonymizer Inc. and others.

# Mixmaster may be redistributed and modified under certain conditions.
# This software is distributed on an "AS IS" basis, WITHOUT WARRANTY OF
# ANY KIND, either express or implied. See the file COPYRIGHT for
# details.

# $Id: Makefile.in 647 2003-10-25 23:34:13Z weasel $

# Edit according to the libraries you want to use:
INC =
#INC = -I/usr/include
DEF = -DUSE_ZLIB -DUSE_PCRE -DUSE_AES -DUSE_NCURSES -DHAVE_NCURSES_H -DUSE_SOCK -DHAVE_SETENV
LIBS = -lz -lpcre -lcrypto -lncurses

# Set compiler options
# Note: Define NDEBUG to disable "assert" when not using DEBUG

OPT = -g -O3 -flto=auto -DNDEBUG
# OPT = -g -pg -DDEBUG

CFLAGS = $(INC) $(DEF) $(OPT) -Wall -Wno-unused-result -Wno-pointer-sign -Wno-discarded-qualifiers -ffunction-sections -fdata-sections
LDFLAGS = $(OPT) -Wl,-gc-sections,-print-gc-sections $(LIBS)
#CC = gcc
#AR = ar
#RANLIB = ranlib
#MAKE = make

OBJ = mix.o rem.o rem1.o rem2.o chain.o chain1.o chain2.o nym.o pgp.o pgpdb.o pgpdata.o pgpget.o pgpcreat.o pool.o mail.o rfc822.o mime.o keymgt.o compress.o stats.o crypto.o random.o util.o buffers.o maildir.o parsedate.tab.o

MIXOBJ = rndseed.o menu.o menusend.o menunym.o menuutil.o menustats.o
NOMENUOBJ = rndseed.o dummy.o
WINOBJ = winmain.o winutil.o

all:	mixmaster

mixmaster: $(OBJ) $(MIXOBJ) main.o
	$(CC) $(OBJ) $(MIXOBJ) main.o $(LDFLAGS) -o mixmaster

libmix.a: $(OBJ) $(MIXOBJ) dllmain.o
	$(AR) rc libmix.a $(OBJ) $(MIXOBJ) dllmain.o

libmix32.a: libmix.a mixlib.def
	dllwrap --dllname mixlib.dll --def mixlib.def --output-lib libmix32.a libmix.a zlib-1.1.4/libz.a pcre-2.08/libpcre.a openssl/libeay32.a -lwsock32

dllmix: main.o libmix32.a
	$(CC) main.o libmix32.a -o dllmix

winmix.exe:	$(WINOBJ) libmix32.a
	$(CC) $(WINOBJ) libmix32.a -lgdi32 -luser32 $(LDFLAGS) -o mixmaster.exe

winmix: winmenu.res #winmix.exe
	rsrc winmenu.res mixmaster.exe

winmenu.o:	winmenu.rc winmenu.h
	windres winmenu.rc winmenu.o

remailer:	$(OBJ) $(NOMENUOBJ) remailer.o
	$(CC) $(OBJ) $(NOMENUOBJ) remailer.o $(LDFLAGS) -o remailer

mpgp:	$(OBJ) $(NOMENUOBJ) mpgp.o
	$(CC) $(OBJ) $(NOMENUOBJ) mpgp.o $(LDFLAGS) -o mpgp

test:	$(OBJ) test.o $(NOMENUOBJ)
	$(CC) $(OBJ) test.o $(NOMENUOBJ) $(LDFLAGS) -o test

clean:
	-rm -f *.o *.a *.res *~ mixmaster mix *.exe remailer test mpgp core gmon.out parsedate parsedate.tab.c

allclean: clean
	-rm -f Makefile

distclean:	allclean

parsedate.tab.c: parsedate.y
	@echo Expect 6 shift/reduce conflicts
	bison parsedate.y

parsedate: parsedate.tab.c
	gcc -DTEST parsedate.tab.c -o parsedate

#install: debian/usr/bin/mixmaster
#	echo TRYING INSTALL debian/usr/bin/mixmaster to /usr/bin/mixmaster
#	install debian/usr/bin/mixmaster /usr/bin/

include Makefile.deps
