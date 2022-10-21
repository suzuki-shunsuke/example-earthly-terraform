VERSION 0.6
FROM alpine:3.16.2
WORKDIR /workspace
ENV PATH=/root/.local/share/aquaproj-aqua/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
COPY aqua.yaml .

install-aqua:
    RUN apk add curl bash
    RUN curl -sSfL https://raw.githubusercontent.com/aquaproj/aqua-installer/v1.1.2/aqua-installer | bash
    RUN aqua i -l
    SAVE ARTIFACT /root/.local/share/aquaproj-aqua

tf-init:
    COPY +install-aqua/aquaproj-aqua /
    COPY foo foo
    WORKDIR /workspace/foo
    RUN terraform init -input=false
    SAVE ARTIFACT .terraform AS LOCAL foo/.terraform

tf-validate:
    COPY +install-aqua/aquaproj-aqua /
    WORKDIR /workspace/foo
    COPY . .
    COPY +tf-init/.terraform .
    RUN terraform validate
