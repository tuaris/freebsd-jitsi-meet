# $FreeBSD: $

PORTNAME=	jitsi-videobridge
DISTVERSIONSUFFIX=	-gfdce823f
DISTVERSION=	2.1-164
CATEGORIES=	net java

MAINTAINER=	daniel@morante.net
COMMENT=	Jitsi Videobridge is a WebRTC compatible video router or SFU.

LICENSE=	APACHE20
LICENSE_FILE=	${WRKSRC}/LICENSE

BUILD_DEPENDS=	mvn:devel/maven

USE_JAVA=	yes
USE_GITHUB=	yes
GH_ACCOUNT=	jitsi

NO_ARCH=	yes

# to rebuild the deps archive:
#   1. set DEV_UPDATE_MODE=yes
#   2. make makesum build
#   3. upload the *-deps archive
#   4. set DEV_UPDATE_MODE=no
#   5. make clean makesum

DEV_UPDATE_MODE=	yes

.if (${DEV_UPDATE_MODE} == "yes")
post-build:
	#@cd ${WRKDIR} && ${TAR} czf ${DISTDIR}/${PORTNAME}-${DISTVERSION}-deps${EXTRACT_SUFX} .m2
	@cd ${WRKDIR} && ${TAR} czf ${PORTNAME}-${DISTVERSION}-deps${EXTRACT_SUFX} .m2
	#@${ECHO} "(!!!) Please upload the maven deps archive: ${DISTDIR}/${PORTNAME}-${DISTVERSION}-deps${EXTRACT_SUFX}"
	@${ECHO} "(!!!) Please upload the maven deps archive: ${WRKDIR}/${PORTNAME}-${DISTVERSION}-deps${EXTRACT_SUFX}"
.else
MASTER_SITES+=	http://ftp.morante.net/pub/FreeBSD/extra/jitsi-videobridge/:maven
DISTFILES+=	${PORTNAME}-${DISTVERSION}-deps${EXTRACT_SUFX}:maven
MVN_ARGS=	--offline
.endif

PLIST_FILES=	${JAVAJARDIR}/jitsi-videobridge-${PORTVERSION:R}-shaded.jar

do-build:
	@cd ${WRKSRC} && ${SETENV} ${MAKE_ENV} \
		${LOCALBASE}/bin/mvn ${MVN_ARGS} \
		-fae install \
		-Dmaven.test.skip=true \
		-Dassembly.skipAssembly=false \
		-Duser.home=${WRKDIR} \
		package
do-install:
	${INSTALL_DATA} ${WRKSRC}/target/${PORTNAME}-${PORTVERSION:R}-shaded.jar ${STAGEDIR}${JAVAJARDIR}
	@(echo "#!/bin/sh"; \
	  echo ""; \
	  echo "mkdir -p ~/.local/share/jitsi-videobridge &&"; \
	  echo "cd ~/.local/share/jitsi-videobridge && ${JAVA} -jar ${JAVAJARDIR}/${PORTNAME}-${PORTVERSION:R}-shaded.jar \""$$"@\"" \
	) > ${STAGEDIR}${PREFIX}/bin/${PORTNAME}
	@${CHMOD} +x ${STAGEDIR}${PREFIX}/bin/${PORTNAME}

.include <bsd.port.mk>
