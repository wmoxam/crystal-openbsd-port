# $OpenBSD$

# Hopefully ${CLANG_ARCHS} at some point.
ONLY_FOR_ARCHS=	amd64

V =		0.26.1
V_SHARDS =	0.8.1
COMMENT =	statically typed object oriented language
DISTNAME =	crystal-${V}
CATEGORIES =	lang

HOMEPAGE =	https://crystal-lang.org/
MAINTAINER =	Wesley Moxam <wes@wmoxam.com>

# Apache 2.0
PERMIT_PACKAGE_CDROM =	Yes

WANTLIB += ${COMPILER_LIBCXX} c event_core event_extra gc iconv
WANTLIB += m pcre yaml

# Requires a bootstrap compiler object (crystal.o) which can be built by:
# .build/crystal build --cross-compile \
# 	--target amd64-unknown-openbsd`uname -r` src/compiler/crystal.cr \
# 	-D i_know_what_im_doing
MASTER_SITES =	https://github.com/crystal-lang/crystal/archive/
MASTER_SITES0 =	https://wmoxam.com/public/
MASTER_SITES1 =	https://github.com/crystal-lang/shards/archive/
DISTFILES =	crystal-${V}{${V}}.tar.gz \
		crystal-${V}-OpenBSD6.3.tar.gz:0 \
		shards-${V}{v${V_SHARDS}}.tar.gz:1

# Build requires llvm-config which only exists in ports-clang, even though the
# clang version is the same as what's in base
COMPILER =	ports-clang

BUILD_DEPENDS =	devel/llvm

LIB_DEPENDS =	converters/libiconv \
		devel/boehm-gc \
		devel/libevent2 \
		devel/pcre \
		devel/libyaml

USE_GMAKE =	Yes
ALL_TARGET =	crystal release=1

NO_TEST =	Yes

# clean up for install
post-patch:
	cd ${WRKSRC}/src && find . -type f -name \*.orig -exec rm "{}" \;

do-build:
	mkdir -p ${WRKSRC}/.build
	# Link the compiler from the pre-built bootstrap object
	cd ${WRKSRC} && CXX=${CXX} ${MAKE_PROGRAM} llvm_ext libcrystal
	cd ${WRKSRC} && ${CXX} -rdynamic -o ${WRKBUILD}/.build/crystal \
		${WRKSRC}/../crystal.o \
		${WRKSRC}/src/llvm/ext/llvm_ext.o \
		${WRKSRC}/src/ext/sigfault.o \
		`(llvm-config --libs --system-libs --ldflags 2> /dev/null)` \
		-lstdc++ -lpcre -lgc -lpthread -levent_core -levent_extra \
		-lssl -liconv
	# Use the compiler to re-compile the compiler
	# stack and data size must be cranked or compiler may segfault
	touch ${WRKSRC}/src/compiler/crystal.cr
	cd ${WRKSRC}; \
		ulimit -Sd `ulimit -Hd`; ulimit -Ss `ulimit -Hs`; \
		CRYSTAL_CONFIG_PATH="lib:${TRUEPREFIX}/lib/crystal" \
		CXX=${CXX} ${MAKE_PROGRAM} ${ALL_TARGET}
	cd ${WRKSRC}/../shards-${V_SHARDS} && \
		${MAKE_PROGRAM} CRYSTAL=${WRKSRC}/.build/crystal CRFLAGS=--release \
		CRYSTAL_PATH=${WRKSRC}/src

do-install:
	${INSTALL_DATA_DIR} ${PREFIX}/lib/crystal
	${INSTALL_PROGRAM} ${WRKSRC}/.build/crystal ${PREFIX}/bin
	cd ${WRKSRC}/src && find . -type d -exec ${INSTALL_DATA_DIR} \
		"${PREFIX}/lib/crystal/{}" \;
	cd ${WRKSRC}/src && find . -type f -exec ${INSTALL_DATA} \
		"{}" "${PREFIX}/lib/crystal/{}" \;
	${INSTALL_PROGRAM} ${WRKSRC}/../shards-${V_SHARDS}/bin/shards \
		${PREFIX}/bin

.include <bsd.port.mk>
