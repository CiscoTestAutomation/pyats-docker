FROM python:3.6-slim
MAINTAINER pyATS Support <pyats-support-ext@cisco.com>

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# version definitions
ARG PYATS_VERSION=19.0
ARG PYATS_ROBOT_VERSION=19.0
ARG GENIE_VERSION=19.0
ARG GENIE_ROBOT_VERSION=19.0

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
 && ${WORKSPACE}/bin/pip install --no-cache-dir pyats==${PYATS_VERSION} genie==${GENIE_VERSION} \
 && ${WORKSPACE}/bin/pip install --no-cache-dir pyats.robot==${PYATS_ROBOT_VERSION} \
                                                genie.libs.robot==${GENIE_ROBOT_VERSION} \
                                                unicon[robot] \
 && mkdir ${WORKSPACE}/users && chmod 775 ${WORKSPACE}/users \
 && apt-get remove -y curl build-essential\
 && apt-get autoremove -y\
 && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# declare workspace as a volume to
# persist through image upgrades
VOLUME ${WORKSPACE}

# modify entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/bin/tini", "--", "/entrypoint.sh"]

# default to python shell
WORKDIR ${WORKSPACE}
CMD ["python"]
