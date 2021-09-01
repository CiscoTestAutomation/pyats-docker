FROM python:3.6-alpine AS builder
LABEL stage=builder
# build container for virtualenv

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# version definitions
ARG pyats_version

# copy wheel files into this container
COPY src /src

RUN apk add --no-cache busybox-extras openssh-client
RUN apk add --no-cache --virtual .build-deps  \
		bzip2-dev \
		cargo \
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
		musl-dev \
		ncurses-dev \
		openssl-dev \
		pax-utils \
		readline-dev \
		rust \
		sqlite-dev \
		tcl-dev \
		tk \
		tk-dev \
		util-linux-dev \
		xz-dev \
		zlib-dev
RUN python3 -m venv ${WORKSPACE}
RUN ${WORKSPACE}/bin/pip install --no-cache-dir --upgrade pip setuptools wheel
RUN ${WORKSPACE}/bin/pip install --no-cache-dir /src/*

FROM python:3.6-alpine
LABEL maintainer="pyATS Support <pyats-support-ext@cisco.com>"

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# version definitions
ARG pyats_version

# copy virtualenv containing installed pyats packages into this image
COPY --from=builder ${WORKSPACE} ${WORKSPACE}
RUN mkdir ${WORKSPACE}/users && chmod 775 ${WORKSPACE}/users

# busybox-extras needed for telnet
RUN apk add --no-cache busybox-extras openssh-client

# modify entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
RUN chmod 755 /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

# default to python shell
WORKDIR ${WORKSPACE}
CMD ["python"]
