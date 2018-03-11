# $OpenBSD$

# Hopefully ${CLANG_ARCHS} at some point.
ONLY_FOR_ARCHS =	amd64

V =		0.24.2
V_SHARDS =	0.7.2
COMMENT =	statically typed object oriented language
DISTNAME =	crystal-${V}
CATEGORIES =	lang

HOMEPAGE =	https://crystal-lang.org/
MAINTAINER =	Wesley Moxam <wes@wmoxam.com>

# Apache 2.0
PERMIT_PACKAGE_CDROM =	Yes

WANTLIB += ${COMPILER_LIBCXX} c event_core event_extra gc iconv
WANTLIB += m pcre yaml z

# Requires a pre-built bootstrap compiler.
MASTER_SITES =	https://github.com/crystal-lang/crystal/archive/
MASTER_SITES0 =	http://wmoxam.com/public/
MASTER_SITES1 =	https://github.com/crystal-lang/shards/archive/
DISTFILES =	crystal-${V}{${V}}.tar.gz \
		crystal-${V}-OpenBSD6.2.tar.gz:0 \
		shards-${V}{v${V_SHARDS}}.tar.gz:1

# Only builds with clang
COMPILER =	base-clang ports-clang

BUILD_DEPENDS =	devel/llvm

LIB_DEPENDS =	converters/libiconv \
		devel/boehm-gc \
		devel/libevent2 \
		devel/pcre \
		devel/libyaml

RUN_DEPENDS =	devel/llvm

USE_GMAKE =	Yes

NO_TEST =	Yes

do-build:
	${CXX} -c -o ${WRKSRC}/src/llvm/ext/llvm_ext.o \
		${WRKSRC}/src/llvm/ext/llvm_ext.cc `llvm-config --cxxflags`
	${CC} -c -o ${WRKSRC}/src/ext/sigfault.o ${WRKSRC}/src/ext/sigfault.c

	mkdir -p ${WRKSRC}/.build
	${CXX} ${WRKSRC}/../crystal.o -o ${WRKBUILD}/.build/crystal -rdynamic \
		${WRKSRC}/src/ext/sigfault.o \
		${WRKSRC}/src/llvm/ext/llvm_ext.o \
		`(llvm-config --libs --system-libs --ldflags 2> /dev/null)` \
		-lstdc++ -lpcre -lgc -lpthread -levent_core -levent_extra \
		-lssl -liconv

	cd ${WRKSRC} && ${MAKE_PROGRAM} deps && ${MAKE_PROGRAM} release=1 \
		CC=${CC} CRYSTAL_CONFIG_PATH="lib:${TRUEPREFIX}/lib/crystal"
	cd ${WRKSRC}/../shards-${V_SHARDS} && CRYSTAL_PATH=${WRKSRC}/src \
		CRYSTAL_BIN=${WRKSRC}/.build/crystal ${MAKE_PROGRAM} release

do-install:
	${INSTALL_DATA_DIR} ${PREFIX}/lib/crystal
	${INSTALL_PROGRAM} ${WRKSRC}/.build/crystal ${PREFIX}/bin

	cd ${WRKSRC}/src && find * -type d -exec ${INSTALL_DATA_DIR} \
		"${PREFIX}/lib/crystal/{}" \;
	cd ${WRKSRC}/src && find * -type f -exec ${INSTALL_DATA} \
		"{}" "${PREFIX}/lib/crystal/{}" \;

	${INSTALL_PROGRAM} ${WRKSRC}/../shards-${V_SHARDS}/bin/shards \
		${PREFIX}/bin

.include <bsd.port.mk>
