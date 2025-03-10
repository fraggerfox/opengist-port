PORTNAME=	opengist
PORTVERSION=	1.9.1
DISTVERSIONPREFIX=	v
CATEGORIES=	www textproc
MASTER_SITES=	LOCAL/fox/:js
DISTFILES=	opengist-${DISTVERSION}-node_modules.tgz:js

PATCH_SITES=	https://github.com/fraggerfox/${PORTNAME}/commit/
PATCHFILES=	00fe79804e37f8622db23acd1e61f6212aef1f78.patch:-p1

MAINTAINER=	fox@FreeBSD.org
COMMENT=	Self-hosted pastebin powered by Git, open-source alternative to Github Gist.
WWW=		https://opengist.io/

LICENSE=	AGPLv3

BROKEN_i386=	fails to build

BUILD_DEPENDS=	npm${NODEJS_SUFFIX}>0:www/npm${NODEJS_SUFFIX}
RUN_DEPENDS=	git:devel/git

USES=		go:1.23,modules nodejs:20,build

USE_GITHUB=	nodefault
GH_ACCOUNT=	thomiceli
GH_PROJECT=	opengist
USE_RC_SUBR=	opengist

GO_MODULE=	github.com/thomiceli/opengist
GO_TARGET=	.
GO_BUILDFLAGS=	-tags fs_embed \
		-ldflags="-X github.com/thomiceli/opengist/internal/config.OpengistVersion=v${DISTVERSION}"
GO_ENV+=	GOTOOLCHAIN=local+path

GROUP=		opengist
USER=		opengist

post-fetch:
	@${FETCH_CMD} -o ${DISTDIR}/${DIST_SUBDIR}/patched.mod https://raw.githubusercontent.com/fraggerfox/opengist/refs/heads/introduce-ldap-authentication/go.mod
	cp ${DISTDIR}/${DIST_SUBDIR}/patched.mod ${DISTDIR}/${DIST_SUBDIR}/v1.9.1.mod

post-extract:
	@${RLN} ${WRKDIR}/node_modules ${WRKSRC}/node_modules

post-patch:
	@${REINPLACE_CMD} -e 's|opengist.db|file:/var/db/${PORTNAME}/opengist.db|g' \
		${WRKSRC}/config.yml
	@${ECHO_MSG} "===> Tidying ${GO_MODNAME} dependencies 2";
	@(cd ${GO_WRKSRC}; ${SETENVI} ${WRK_ENV} ${MAKE_ENV} ${GO_ENV} \
		GOPROXY=${GO_MODCACHE} ${GO_CMD} mod tidy -e)
	@${ECHO_MSG} "===> Vendoring ${GO_MODNAME} dependencies 2";
	@(cd ${GO_WRKSRC}; ${SETENVI} ${WRK_ENV} ${MAKE_ENV} ${GO_ENV} \
		GOPROXY=${GO_MODCACHE} ${GO_CMD} mod vendor -e)

pre-build:
	cd ${WRKSRC} && \
		${SETENV} ${MAKE_ENV} npx --offline vite -c public/vite.config.js \
		build
	cd ${WRKSRC} && \
		${SETENV} ${MAKE_ENV} EMBED=1 npx --offline postcss 'public/assets/embed-*.css' \
		-c public/postcss.config.js --replace

post-install:
	${MKDIR} ${STAGEDIR}${PREFIX}/etc/${PORTNAME}
	${INSTALL_DATA} ${WRKSRC}/config.yml \
		${STAGEDIR}${PREFIX}/etc/${PORTNAME}/config.yml.sample
	${MKDIR} ${STAGEDIR}/var/db/${PORTNAME}
	${MKDIR} ${STAGEDIR}/var/run/${PORTNAME}
	${MKDIR} ${STAGEDIR}${WWWDIR}/assets
	cd ${WRKSRC} && \
		${INSTALL_DATA} public/assets/* \
		${STAGEDIR}${WWWDIR}/assets
	cd ${WRKSRC} && \
		${INSTALL_DATA} public/manifest.json \
		${STAGEDIR}${WWWDIR}

#
# To update the opengist-${DISTVERSION}-node_modules.tgz archive:
# 1. Comment out the DISTFILES and post-extract lines
# 2. Run 'make makesum regenerate-node_modules-distfile clean'
# 3. Upload the archive
# 4. Uncomment the lines from step #1
# 5. Run 'make makesum'
#

regenerate-node_modules-distfile: patch
	cd ${WRKSRC} && \
		${RM} -r node_modules && \
		${MAKE_ENV} npm install --prefix ${WRKSRC} && \
		${MAKE_ENV} npm install --prefix ${WRKSRC} --os freebsd --cpu \
		amd64 esbuild@0.18.20 && \
		${MAKE_ENV} npm install --prefix ${WRKSRC} --os freebsd --cpu \
		arm64 esbuild@0.18.20 && \
		${TAR} czf ${DISTDIR}/opengist-${DISTVERSION}-node_modules.tgz \
		node_modules && \
		${ECHO} "Please upload the file ${DISTDIR}/opengist-${DISTVERSION}-node_modules.tgz"

.include <bsd.port.mk>
