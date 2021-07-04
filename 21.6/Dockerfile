FROM python:3.6-slim AS builder
LABEL stage=builder
# build container for virtualenv

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# version definition
ARG PYATS_VERSION=21.6

# create virtualenv and install pyats packages
RUN apt-get update
RUN apt-get install -y --no-install-recommends iputils-ping telnet openssh-client curl build-essential
RUN pip3 install --upgrade --no-cache-dir setuptools pip virtualenv
RUN virtualenv ${WORKSPACE}
RUN ${WORKSPACE}/bin/pip install --no-cache-dir --upgrade pip setuptools wheel
RUN ${WORKSPACE}/bin/pip install --no-cache-dir pyats~=${PYATS_VERSION}.0 genie~=${PYATS_VERSION}.0

# tini version
ENV TINI_VERSION 0.18.0

RUN curl -fsSL https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini-static-amd64 -o /bin/tini
RUN chmod +x /bin/tini

FROM python:3.6-slim
LABEL maintainer="pyATS Support <pyats-support-ext@cisco.com>"

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# version definition
ARG PYATS_VERSION=21.5

# copy virtualenv containing installed pyats packages into this image
COPY --from=builder ${WORKSPACE} ${WORKSPACE}
RUN mkdir ${WORKSPACE}/users && chmod 775 ${WORKSPACE}/users

# use tini as subreaper in Docker container to adopt zombie processes
COPY --from=builder /bin/tini /bin/tini

# install common tools
RUN apt-get update \
 && apt-get install -y --no-install-recommends iputils-ping telnet openssh-client \
 && rm -rf /var/lib/apt/lists/*

# modify entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/bin/tini", "--", "/entrypoint.sh"]

# default to python shell
WORKDIR ${WORKSPACE}
CMD ["python"]
