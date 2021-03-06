FROM python:3.6-alpine
MAINTAINER pyATS Support <pyats-support-ext@cisco.com>

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# version definitions
ARG PYATS_VERSION=21.3

# copy wheel files into this container
ADD src /src

# 1. install common tools
# 2. install build dependencies
# 3. create virtual environment
#    - install pyats
#    - create user directory
#    - delete added unwanted files
# 4. cleanup
RUN apk add --no-cache busybox-extras openssh-client \
 && apk add --no-cache --virtual .build-deps  \
		bzip2-dev \
		coreutils \
		dpkg-dev dpkg \
		expat-dev \
		findutils \
		gcc \
		gdbm-dev \
		libc-dev \
		libffi-dev \
		libnsl-dev \
		libtirpc-dev \
		linux-headers \
		make \
		ncurses-dev \
		openssl-dev \
		pax-utils \
		readline-dev \
		sqlite-dev \
		tcl-dev \
		tk \
		tk-dev \
		util-linux-dev \
		xz-dev \
		zlib-dev \
 && python3 -m venv ${WORKSPACE} \
 && ${WORKSPACE}/bin/pip install --no-cache-dir --upgrade pip setuptools \
 && PYATS_WHL=`ls /src/pyats-* | head -n 1` \
 && ${WORKSPACE}/bin/pip install --no-cache-dir "$PYATS_WHL[full]" /src/* \
 && mkdir ${WORKSPACE}/users && chmod 775 ${WORKSPACE}/users \
 && rm -rf /src \
 && apk del .build-deps

# modify entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

# default to python shell
WORKDIR ${WORKSPACE}
CMD ["python"]
