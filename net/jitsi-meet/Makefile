# $FreeBSD: $

PORTNAME=	jitsi-meet
DISTVERSIONPREFIX=	${PORTNAME}_
DISTVERSION=	4424
CATEGORIES=	net java
MASTER_SITES+=	https://raw.githubusercontent.com/${GH_ACCOUNT}/${PORTNAME}/${DISTVERSIONPREFIX}${DISTVERSION}/:packagejson
DISTFILES+=		package-lock.json:packagejson package.json:packagejson
#DISTFILES+=		npm-cache-${DISTVERSION}.tar.bz2:npmcache
DIST_SUBDIR=	${DISTVERSIONPREFIX}${DISTVERSION}
EXTRACT_ONLY=	${DISTNAME}${EXTRACT_SUFX} \
				npm-cache-${DISTVERSION}.tar.bz2

MAINTAINER=	daniel@morante.net
COMMENT=	Secure, Simple and Scalable Video Conferences

LICENSE=	APACHE20
LICENSE_FILE=	${WRKSRC}/LICENSE

BUILD_DEPENDS+=	node:www/node \
				libuv>0:devel/libuv

FETCH_DEPENDS+=	node:www/node

USE_GITHUB=	yes
GH_ACCOUNT=	jitsi

USES=	python:2.7,build gmake

CXXFLAGS+=	-I${LOCALBASE}/include

BINARY_ALIAS=   python2=${PYTHON_CMD}

post-fetch:
	# Make an offline npm cache usng the 'ci' command and avoid running any
	# tasks that are best handled during build (--ignore-scripts)
	@if [ ! -f ${DISTDIR}/${DIST_SUBDIR}/npm-cache-${DISTVERSION}.tar.bz2 ]; then \
		cd ${DISTDIR}/${DIST_SUBDIR} && \
			npm_config_cache=${DISTDIR}/${DIST_SUBDIR}/npm_cache \
			npm_config_prefix=${DISTDIR}/${DIST_SUBDIR}/node_modules \
			${LOCALBASE}/bin/npm ci --ignore-scripts --nodedir=${LOCALBASE}; \
		tar -a -cf ${DISTDIR}/${DIST_SUBDIR}/npm-cache-${DISTVERSION}.tar.bz2 -C ${DISTDIR}/${DIST_SUBDIR} npm_cache node_modules && \
		rm -rf ${DISTDIR}/${DIST_SUBDIR}/npm_cache; rm -rf ${DISTDIR}/${DIST_SUBDIR}/node_modules; \
	fi

post-extract:	
	@mv ${WRKDIR}/node_modules ${WRKSRC}/node_modules

do-build:
	# The 'rebuild' command has to be used instead of install since we 
	# used '--ignore-scripts' earlier
	cd ${WRKSRC} && ${SETENV} ${MAKE_ENV} PATH=${PATH}:${WRKSRC}/node_modules/.bin \
		npm_config_cache=${WRKDIR}/.npm_cache \
		npm_config_build_from_source=true \
		npm_config_offline=true \
		${LOCALBASE}/bin/npm rebuild --offline --nodedir=${LOCALBASE}
	cd ${WRKSRC} && ${SETENV} ${MAKE_ENV} make

.include <bsd.port.mk>
