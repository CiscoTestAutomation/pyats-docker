FROM python:3.6-slim
MAINTAINER pyATS Support <pyats-support-ext@cisco.com>

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# version definition
ARG PYATS_VERSION=21.5

# tini version
ENV TINI_VERSION 0.18.0

# 1. install common tools
# 2. use tini as subreaper in Docker container to adopt zombie processes
# 3. update packages in system python
# 4. create virtual environment
#    - install pyats packages
#    - create user directory
RUN apt-get update \
 && apt-get install -y --no-install-recommends iputils-ping telnet openssh-client curl build-essential\
 && curl -fsSL https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini-static-amd64 -o /bin/tini \
 && chmod +x /bin/tini \
 && pip3 install --upgrade --no-cache-dir setuptools pip virtualenv \
 && virtualenv ${WORKSPACE} \
 && ${WORKSPACE}/bin/pip install --no-cache-dir pyats[full]~=${PYATS_VERSION}.0 rest.connector~=${PYATS_VERSION}.0 yang.connector~=${PYATS_VERSION}.0 \
 && mkdir ${WORKSPACE}/users && chmod 775 ${WORKSPACE}/users \
 && apt-get remove -y curl build-essential\
 && apt-get autoremove -y\
 && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# modify entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/bin/tini", "--", "/entrypoint.sh"]

# default to python shell
WORKDIR ${WORKSPACE}
CMD ["python"]
