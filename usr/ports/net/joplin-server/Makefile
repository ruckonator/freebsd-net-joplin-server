PORTNAME=	joplin
DISTVERSIONPREFIX=	server-v
DISTVERSION=	3.7.4
CATEGORIES=	net
MASTER_SITES=	https://github.com/ruckonator/freebsd-net-joplin-server/raw/refs/heads/tarballs/node-modules/:prefetch \
				https://nodejs.org/download/release/v24.22.2/:node_headers
PKGNAMESUFFIX=	-server
DISTFILES=		${PORTNAME}${PKGNAMESUFFIX}-${DISTVERSION}-node-modules${EXTRACT_SUFX}:prefetch \
				node-v24.22.2-headers${EXTRACT_SUFX}:node_headers

MAINTANIER=	freebsd@perpetualthought.com
COMMENT=	Server Backend for the Joplin Notetaking application
WWW=		https://joplinapp.org/

LICENSE=	MIT

FETCH_DEPENDS=	npm${NODEJS_SUFFIX}>0:www/npm${NODEJS_SUFFIX}
BUILD_DEPENDS=	npm${NODEJS_SUFFIX}>0:www/npm${NODEJS_SUFFIX}
LIB_DEPENDS=	libvips.so:graphics/vips

USES=		nodejs:24,build,run npm:yarn4,build python:build

USE_GITHUB=		yes
GH_ACCOUNT=		laurent22

USE_RC_SUBR=	${PORTNAME}

USERS=		joplin
GROUPS=		joplin
MAKE_ENV+=	npm_config_nodedir=${WRKDIR}/node-v24.22.2 SHARP_FORCE_GLOBAL_LIBVIPS=1 BUILD_SEQUENCIAL=1 \
			YARN_ENABLE_GLOBAL_CACHE=false YARN_ENABLE_NETWORK=false YARN_CACHE_FOLDER=${WRKDIR}/node-modules-cache

BINARY_ALIAS=	python3=${PYTHON_CMD}

TARGET_ORDER_OVERRIDE=	750:npm-install-node-modules

post-extract:
	# Copy over only the packages needed for joplin server
	${MKDIR} ${WRKDIR}/${PORTNAME}/.yarn
	${MKDIR} ${WRKDIR}/${PORTNAME}/packages/fork-sax
	${CP} ${WRKSRC}/gulpfile.js ${WRKDIR}/${PORTNAME}/
	${CP} ${WRKSRC}/lerna.json ${WRKDIR}/${PORTNAME}/
	${CP} ${WRKSRC}/package.json ${WRKDIR}/${PORTNAME}/
	${CP} ${WRKSRC}/tsconfig.json ${WRKDIR}/${PORTNAME}/
	${CP} ${WRKSRC}/yarn.lock ${WRKDIR}/${PORTNAME}/
	${CP} ${WRKSRC}/.env-sample ${WRKDIR}/joplin.conf.sample
	${CP} ${WRKSRC}/.yarnrc.yml ${WRKDIR}/${PORTNAME}/
	${CP} -r ${WRKSRC}/packages/fork-htmlparser2 ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/packages/fork-sax/package.json ${WRKDIR}/${PORTNAME}/packages/fork-sax/
	${CP} -r ${WRKSRC}/packages/fork-uslug ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/packages/htmlpack ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/packages/lib ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/packages/renderer ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/packages/turndown-plugin-gfm ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/packages/turndown ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/packages/server ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/packages/tools ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/packages/utils ${WRKDIR}/${PORTNAME}/packages/
	${CP} -r ${WRKSRC}/.yarn/releases ${WRKDIR}/${PORTNAME}/.yarn
	${CP} -r ${WRKSRC}/.yarn/patches ${WRKDIR}/${PORTNAME}/.yarn
	${RM} -rf ${WRKSRC}
	${MV} ${WRKDIR}/${PORTNAME} ${WRKSRC}

do-build:
	# Build Joplin-Server
	(cd ${WRKSRC} && \
	    ${SETENV} ${MAKE_ENV} ${NPM_CMDNAME} install --inline-builds && \
	    ${SETENV} ${MAKE_ENV} ${NPM_CMDNAME} workspaces focus --production --all)

do-install:
	${MKDIR} ${STAGEDIR}${ETCDIR} \
		${STAGEDIR}${PREFIX}/libexec/${PORTNAME}${PKGNAMESUFFIX}
	(cd ${WRKSRC} && \
		${COPYTREE_SHARE} node_modules ${STAGEDIR}${PREFIX}/libexec/${PORTNAME}${PKGNAMESUFFIX})
	(cd ${WRKSRC} && \
		${COPYTREE_SHARE} packages ${STAGEDIR}${PREFIX}/libexec/${PORTNAME}${PKGNAMESUFFIX})
	(cd ${WRKSRC} && \
		${COPYTREE_SHARE} package.json ${STAGEDIR}${PREFIX}/libexec/${PORTNAME}${PKGNAMESUFFIX})
	(cd ${WRKSRC} && \
		${COPYTREE_SHARE} yarn.lock ${STAGEDIR}${PREFIX}/libexec/${PORTNAME}${PKGNAMESUFFIX})
	${INSTALL_DATA} ${WRKDIR}/joplin.conf.sample \
		${STAGEDIR}${ETCDIR}/joplin.conf.sample

.include <bsd.port.mk>
