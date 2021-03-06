#	$OpenBSD: Makefile,v 1.1 2016/12/17 23:38:33 patrick Exp $

NOMAN=		#

.if ${MACHINE} == "arm64"

PROG=		BOOTAA64.EFI
OBJFMT=		binary
INSTALL_STRIP=
BINDIR=		/usr/mdec
SRCS=		start.S self_reloc.c efiboot.c conf.c exec.c efidev.c fdt.c

S=		${.CURDIR}/../../../..
EFIDIR=		${S}/stand/efi

OBJCOPY?=	objcopy
OBJDUMP?=	objdump

LDFLAGS+=-nostdlib -T ${.CURDIR}/ldscript.arm64 -Bsymbolic -shared

.PATH:	${S}/stand/boot
SRCS+=	boot.c cmd.c vars.c

.PATH:	${S}/lib/libsa
SRCS+=	alloc.c ctime.c exit.c getchar.c memcmp.o memcpy.o memmove.c memset.c \
	printf.c putchar.c snprintf.c strchr.c strcmp.c strerror.c strncmp.c \
	strncpy.c strtol.c
SRCS+=	close.c closeall.c cons.c cread.c dev.c disklabel.c dkcksum.c fstat.c \
	lseek.c open.c read.c readdir.c stat.c
SRCS+=	loadfile.c
SRCS+=	ufs.c

.PATH:	${S}/lib/libkern/arch/arm64 ${S}/lib/libkern
SRCS+=	divdi3.c moddi3.c qdivrem.c strlcpy.c strlen.c

.PATH:	${S}/lib/libz
SRCS+=	adler32.c crc32.c inflate.c inftrees.c

CPPFLAGS+=	-nostdinc
CPPFLAGS+=	-I${S} -I. -I${.CURDIR}
CPPFLAGS+=	-I${EFIDIR}/include -I${EFIDIR}/include/arm64
CPPFLAGS+=	-D_STANDALONE
CPPFLAGS+=	-DSMALL -DSLOW -DNOBYFOUR -D__INTERNAL_LIBSA_CREAD
CPPFLAGS+=	-DNEEDS_HEAP_H
COPTS+=		-Wno-attributes -Wno-format
COPTS+=		-ffreestanding -fno-stack-protector
COPTS+=		-fshort-wchar -fPIC -fno-builtin
COPTS+=		-Wall -Werror

PROG.elf=	${PROG:S/.EFI/.elf/}
CLEANFILES+=	${PROG.elf} ${PROG.elf}.tmp

${PROG}: ${PROG.elf}
	${OBJCOPY} -j .peheader -j .text -j .sdata -j .data \
		-j .dynamic -j .dynsym -j .dynstr -j .rel -j .rel.dyn \
		-j .rela -j .rela.dyn -j .reloc \
		--output-target=${OBJFMT} ${PROG.elf} ${.TARGET}

.include <bsd.prog.mk>

${PROG.elf}: ${OBJS}
	${LD} ${LDFLAGS} -o ${.TARGET}.tmp ${OBJS} ${LDADD}
	@if ${OBJDUMP} -t ${.TARGET}.tmp | grep 'UND'; then	\
		(echo Undefined symbols; false);		\
	fi
	mv ${.TARGET}.tmp ${.TARGET}

.if !make(clean) && !make(cleandir) && !make(includes) && !make(obj)
.BEGIN:
	@([ -h machine ] || ln -s ${.CURDIR}/../../../${MACHINE}/include machine)
.NOPATH: machine
CLEANFILES+=	machine
.endif

.else
NOPROG=yes
.include <bsd.prog.mk>
.endif
