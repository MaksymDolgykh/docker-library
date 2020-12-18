FROM alpine:3.12.3

ARG KUBECTL_VERSION="v1.20.0"

RUN apk add --no-cache curl bash

RUN curl -o /usr/local/bin/kubectl \
    -LO https://storage.googleapis.com/kubernetes-release/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl

RUN chmod +x /usr/local/bin/kubectl

ENTRYPOINT ["/bin/bash"]
