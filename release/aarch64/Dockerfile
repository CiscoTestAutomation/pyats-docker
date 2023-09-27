FROM arm64v8/python:3.10 as builder
LABEL stage=builder

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# version definition
ARG pyats_version
RUN echo "Building $pyats_version"

# copy wheel files into this container
ADD src /src

# create virtualenv and install pyats packages
RUN apt-get update
RUN apt-get install -y build-essential virtualenv
RUN virtualenv ${WORKSPACE}
RUN ${WORKSPACE}/bin/pip install --upgrade --no-cache-dir pip setuptools wheel
RUN ${WORKSPACE}/bin/pip --no-cache-dir /src/*

# tini version
ENV TINI_VERSION 0.18.0

RUN curl -fsSL https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini-static-arm64 -o /bin/tini
RUN chmod +x /bin/tini

FROM arm64v8/python:3.9
LABEL maintainer="pyATS Support <pyats-support-ext@cisco.com>"

# workspace location
ARG WORKSPACE
ENV WORKSPACE ${WORKSPACE:-/pyats}

# version definition
ARG pyats_version

# copy virtualenv containing installed pyats pacakges into this image
COPY --from=builder ${WORKSPACE} ${WORKSPACE}
RUN mkdir ${WORKSPACE}/users && chmod 775 ${WORKSPACE}/users

# use tini as subreaper in Docker container to adopt zombie processes
COPY --from=builder /bin/tini /bin/tini

# install common tools
RUN apt-get update
RUN apt-get install -y --no-install-recommends iputils-ping telnet openssh-client git \
    && rm -rf /var/lib/apt/lists/*

# modify entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
RUN chmod 755 /entrypoint.sh
ENTRYPOINT ["/bin/tini", "--", "/entrypoint.sh"]

# default to python shell
WORKDIR ${WORKSPACE}
CMD ["python"]
