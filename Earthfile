VERSION 0.6
FROM alpine:3.16.2
WORKDIR /workspace
ENV PATH=/root/.local/share/aquaproj-aqua/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
COPY aqua.yaml .
RUN apk add curl bash
RUN curl -sSfL https://raw.githubusercontent.com/aquaproj/aqua-installer/v1.1.2/aqua-installer | bash
RUN aqua i -l

tf-init:
    COPY foo foo
    WORKDIR /workspace/foo
    RUN terraform init -input=false
    SAVE ARTIFACT .terraform AS LOCAL foo/.terraform
    SAVE ARTIFACT .terraform.lock.hcl AS LOCAL foo/.terraform.lock.hcl

tf-validate:
    COPY foo foo
    COPY +tf-init/.terraform foo/.terraform
    COPY +tf-init/.terraform.lock.hcl foo/.terraform.lock.hcl
    WORKDIR /workspace/foo
    RUN terraform validate
