# $OpenBSD: Makefile.template,v 1.75 2016/03/20 17:19:49 naddy Exp $

# Reasons why the port/package shouldn't be built
#
ONLY_FOR_ARCHS =	amd64

COMMENT =	statically typed object oriented language

VERSION  =      0.20.5
DISTNAME =	crystal-${VERSION}
PKGNAME  =	crystal-${VERSION}

CATEGORIES =	lang
HOMEPAGE   =	https://crystal-lang.org/
MAINTAINER =    wes@wmoxam.com

# Apache License, v2.0
PERMIT_PACKAGE_CDROM =	Yes

WANTLIB += c event_core event_extra gc iconv m pcre pthread z

MASTER_SITES  =		https://github.com/crystal-lang/crystal/archive/
MASTER_SITES0 = 	http://wmoxam.com/public/
DISTFILES     =		${VERSION}.tar.gz \
			crystal-${VERSION}.tar.gz:0

NO_CONFIGURE =		Yes
USE_GMAKE    =		Yes

RUN_DEPENDS =		converters/libiconv \
			devel/boehm-gc \
			devel/libevent2 \
			devel/pcre \
			shells/bash

MODULES	       =	lang/clang
MODCLANG_ARCHS =	amd64
MODCLANG_LANGS =	c c++

do-build:
	clang++ -c -o ${WRKSRC}/src/llvm/ext/llvm_ext.o ${WRKSRC}/src/llvm/ext/llvm_ext.cc `llvm-config --cxxflags` 
	clang -c -o ${WRKSRC}/src/ext/sigfault.o ${WRKSRC}/src/ext/sigfault.c `llvm-config --cflags`

	mkdir ${WRKSRC}/.build
	clang++ ${WRKSRC}/../crystal.o -o ${WRKSRC}/.build/crystal -rdynamic  ${WRKSRC}/src/ext/sigfault.o ${WRKSRC}/src/llvm/ext/llvm_ext.o `(llvm-config --libs --system-libs --ldflags 2> /dev/null)` -lstdc++ -lpcre -lgc -lpthread -levent_core -levent_extra -lssl -liconv

	cd ${WRKSRC} && gmake deps && CC=clang gmake

do-install:
	${INSTALL_DATA} ${FILESDIR}/crystal ${WRKSRC}
	${SUBST_CMD} ${WRKSRC}/crystal
	${INSTALL_SCRIPT} ${WRKSRC}/crystal ${PREFIX}/bin
	${INSTALL_DATA_DIR} ${PREFIX}/lib/crystal
	${INSTALL_DATA_DIR} ${PREFIX}/lib/crystal/bin
	${INSTALL_PROGRAM} ${WRKSRC}/.build/crystal ${PREFIX}/lib/crystal/bin
	cp -R ${WRKSRC}/src/ ${PREFIX}/lib/crystal/src/

.include <bsd.port.mk>
