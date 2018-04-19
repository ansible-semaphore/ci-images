# Ansible Semaphore CI Image
# This bundles most of the tools we use so we dont need to install them in the ci
FROM circleci/golang:1.10

ENV TASK_VERSION=2.0.1 \
    NVM_VERSION=0.33.5 \
    NODE_VERSION=8.2.0 \
    DREDD_VERSION=5.1.5 \
    GORELEASER_VERSION=0.66.1

# hadolint ignore=DL3002
USER 0
# hadolint ignore=DL3008
RUN apt-get install -y --no-install-recommends rpm

USER circleci

WORKDIR /go/bin
# hadolint ignore=SC1090
RUN mkdir -p /go/src/github.com/ansible-semaphore/semaphore && \
    curl -L "https://github.com/go-task/task/releases/download/v${TASK_VERSION}/task_linux_amd64.tar.gz" | tar xvz && \
    curl -o- "https://raw.githubusercontent.com/creationix/nvm/v${NVM_VERSION}/install.sh" | bash && \
    export NVM_DIR="${HOME}/.nvm" && \
    [ -s "${NVM_DIR}/nvm.sh" ] && \. "${NVM_DIR}/nvm.sh" && \
    nvm install "${NODE_VERSION}" && nvm alias default "${NODE_VERSION}" && \
    npm install -g "dredd@${VERSION}"
    #&& \
    # Each step uses the same `$BASH_ENV`, so need to modify it
#    echo 'export NVM_DIR="$HOME/.nvm"' >> $BASH_ENV && \
#    echo "[ -s \"$NVM_DIR/nvm.sh\" ] && . \"$NVM_DIR/nvm.sh\"" >> $BASH_ENV

WORKDIR /go/src/github.com/ansible-semaphore/semaphore
# This is pretty much the same as semaphores deps:tools, and should be updated as that changes
# it does not pull the code and run it directly as that's a bit of circular dependency hell to manage
RUN go get github.com/schrej/godacov && \
    go get -u github.com/golang/dep/cmd/dep && \
    go get -u github.com/gobuffalo/packr/... && \
    go get -u github.com/haya14busa/goverage && \
    go get -u github.com/alecthomas/gometalinter && \
    gometalinter --install && \
    curl -L "https://github.com/goreleaser/goreleaser/releases/download/v${GORELEASER_VERSION}/goreleaser_$(uname -s)_$(uname -m).tar.gz" | tar -xz -C "${GOPATH}/bin"

COPY ./bin /usr/local/bin/
RUN /usr/local/bin/create-default-config

CMD ["/bin/sh"]