FROM alpine:3.8
MAINTAINER pyATS Support <pyats-support-ext@cisco.com>

# install common tools
RUN apk add python3 \
            python3-dev \
            gcc \
            linux-headers \
            musl-dev \
            busybox-extras \
            openssh-client

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# copy wheel files into repo
ADD src /src

# install pyats into system python
RUN pip3 install --no-cache-dir /src/*

# create virtual environment
#  - preserve system package usage
#  - create user directory
#  - symlink examples & templates to workspace
#  - delete added unwanted files
RUN python3 -m venv --system-site-packages ${WORKSPACE} \
 && mkdir ${WORKSPACE}/users && chmod 775 ${WORKSPACE}/users \
 && ln -s /usr/examples ${WORKSPACE}/examples \
 && ln -s /usr/templates ${WORKSPACE}/templates \
 && ln -s /usr/genie_yamls ${WORKSPACE}/genie_yamls \
 && rm -rf /src

# declare workspace as a volume to
# persist through image upgrades
VOLUME ${WORKSPACE}

# modify entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

# default to python shell
WORKDIR ${WORKSPACE}
CMD ["python"]
