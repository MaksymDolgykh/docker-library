# https://github.com/roboll/helmfile#running-as-a-container
ARG HELMFILE_VERSION="helm3-v0.138.7"
FROM quay.io/roboll/helmfile:$HELMFILE_VERSION

# https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html
ARG aws_iam_authenticator_version="1.19.6/2021-01-05"

# https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
ARG kubectl_version="v1.20.0"

ARG pip_version="21.0.1"
ARG aws_cli_version="1.19.53"

WORKDIR /tmp/work

RUN apk update \
    && apk add --no-cache jq python3 \
    && pip3 install --no-cache-dir \
        pip==${pip_version} \
        awscli==${aws_cli_version} \
    && wget --quiet --output-document aws-iam-authenticator "https://amazon-eks.s3-us-west-2.amazonaws.com/${aws_iam_authenticator_version}/bin/linux/amd64/aws-iam-authenticator" \
    && wget --quiet --output-document kubectl "https://dl.k8s.io/release/${kubectl_version}/bin/linux/amd64/kubectl" \
    && chmod +x aws-iam-authenticator kubectl \
    && mv -v aws-iam-authenticator kubectl /usr/local/bin/

COPY /entrypoint /entrypoint
ENTRYPOINT ["/entrypoint"]
