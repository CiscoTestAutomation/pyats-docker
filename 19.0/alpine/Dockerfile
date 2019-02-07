FROM python:3.6-alpine
MAINTAINER pyATS Support <pyats-support-ext@cisco.com>

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# copy wheel files into this container
ADD src /src

# 1. install common tools
# 2. install build dependencies
# 3. create virtual environment
#    - preserve system package usage
#    - create user directory
#    - symlink examples & templates to workspace
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
 && pip3 install --no-cache-dir --no-use-pep517 /src/* \
 && python3 -m venv --system-site-packages ${WORKSPACE} \
 && ${WORKSPACE}/bin/pip install --no-cache-dir --upgrade pip setuptools \
 && mkdir ${WORKSPACE}/users && chmod 775 ${WORKSPACE}/users \
 && ln -s /usr/local/examples ${WORKSPACE}/examples \
 && ln -s /usr/local/templates ${WORKSPACE}/templates \
 && ln -s /usr/local/genie_yamls ${WORKSPACE}/genie_yamls \
 && rm -rf /src \
 && apk del .build-deps

# declare workspace as a volume to
# persist through image upgrades
VOLUME ${WORKSPACE}

# modify entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

# default to python shell
WORKDIR ${WORKSPACE}
CMD ["python"]
