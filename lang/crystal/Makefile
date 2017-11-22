# $OpenBSD: Makefile.template,v 1.75 2016/03/20 17:19:49 naddy Exp

# Reasons why the port/package shouldn't be built
#
ONLY_FOR_ARCHS =	amd64

COMMENT =	statically typed object oriented language

VERSION  =      0.24.0
VERSION_SHARDS = 0.7.2
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
MASTER_SITES1 =         https://github.com/crystal-lang/shards/archive/
DISTFILES     =		${VERSION}.tar.gz \
			crystal-${VERSION}-OpenBSD6.2.tar.gz:0 \
			v${VERSION_SHARDS}.tar.gz:1

NO_CONFIGURE =		Yes
USE_GMAKE    =		Yes

LIB_DEPENDS =           converters/libiconv \
                        devel/boehm-gc \
                        devel/libevent2 \
			devel/llvm \
                        devel/pcre \
			devel/libyaml

do-build:
	clang-5.0 -c -o ${WRKSRC}/src/llvm/ext/llvm_ext.o ${WRKSRC}/src/llvm/ext/llvm_ext.cc `llvm-config --cxxflags`
	clang-5.0 -c -o ${WRKSRC}/src/ext/sigfault.o ${WRKSRC}/src/ext/sigfault.c

	mkdir ${WRKSRC}/.build
	clang-5.0 ${WRKSRC}/../crystal.o -o ${WRKSRC}/.build/crystal -rdynamic  ${WRKSRC}/src/ext/sigfault.o ${WRKSRC}/src/llvm/ext/llvm_ext.o `(llvm-config --libs --system-libs --ldflags 2> /dev/null)` -lstdc++ -lpcre -lgc -lpthread -levent_core -levent_extra -lssl -liconv

	cd ${WRKSRC} && gmake deps && gmake release=1 CC=clang-5.0 CRYSTAL_CONFIG_PATH="lib:/usr/local/lib/crystal"
	cd ${WRKSRC}/../shards-${VERSION_SHARDS} && CRYSTAL_PATH=${WRKSRC}/src CRYSTAL_BIN=${WRKSRC}/.build/crystal gmake release

do-install:
	${INSTALL_DATA_DIR} ${PREFIX}/lib/crystal
	${INSTALL_PROGRAM} ${WRKSRC}/.build/crystal ${PREFIX}/bin
	cp -R ${WRKSRC}/src/* ${PREFIX}/lib/crystal/.
	${INSTALL_PROGRAM} ${WRKSRC}/../shards-${VERSION_SHARDS}/bin/shards ${PREFIX}/bin

.include <bsd.port.mk>
