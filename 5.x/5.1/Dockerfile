FROM python:3.6
MAINTAINER pyATS Support <pyats-support-ext@cisco.com>

# install common tools
RUN apt-get update \
 && apt-get install -y telnet \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# use tini as subreaper in Docker container to adopt zombie processes
ENV TINI_VERSION 0.15.0
RUN curl -fsSL https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini-static-amd64 -o /bin/tini && chmod +x /bin/tini

# update packages in system python
RUN pip3 install --upgrade --no-cache-dir setuptools pip virtualenv

# version definitions
ARG PYATS_VERSION=5.1.0
ARG GENIE_VERSION=3.1.2

# install pyats into system python
RUN pip3 install --no-cache-dir pyats==${PYATS_VERSION} genie==${GENIE_VERSION}

# create virtual environment
#  - preserve system package usage
#  - create user directory
#  - symlink examples & templates to workspace
RUN virtualenv --system-site-packages ${WORKSPACE} \
 && mkdir ${WORKSPACE}/users && chmod 775 ${WORKSPACE}/users \
 && ln -s /usr/local/examples ${WORKSPACE}/examples \
 && ln -s /usr/local/templates ${WORKSPACE}/templates \
 && ln -s /usr/local/genie_yamls ${WORKSPACE}/genie_yamls

# declare workspace as a volume to
# persist through image upgrades
VOLUME ${WORKSPACE}

# modify entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/bin/tini", "--", "/entrypoint.sh"]

# default to python shell
WORKDIR ${WORKSPACE}
CMD ["python"]
