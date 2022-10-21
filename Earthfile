VERSION 0.6
FROM alpine:3.16.2
WORKDIR /workspace
ENV PATH=/root/.local/share/aquaproj-aqua/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
COPY aqua.yaml .
RUN apk add curl bash
RUN curl -sSfL https://raw.githubusercontent.com/aquaproj/aqua-installer/v1.1.2/aqua-installer | bash
RUN aqua i -l
ARG ci
IF [ -z "$ci" ]
    ENV GITHUB_COMMENT_SKIP=true
END

validate-arg-dir:
    ARG dir
    IF [ "$dir" = . ]
        RUN echo "arg dir is required" >&2
        RUN exit 1
    END

tf-init:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    WORKDIR /workspace/$dir
    RUN github-comment exec -- terraform init -input=false
    SAVE ARTIFACT .terraform AS LOCAL $dir/.terraform
    SAVE ARTIFACT .terraform.lock.hcl AS LOCAL $dir/.terraform.lock.hcl

tf-validate:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    COPY +tf-init/.terraform $dir/.terraform
    COPY +tf-init/.terraform.lock.hcl $dir/.terraform.lock.hcl
    WORKDIR /workspace/$dir
    RUN terraform validate

tfsec:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    WORKDIR /workspace/$dir
    RUN tfsec

tflint:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    WORKDIR /workspace/$dir
    RUN tflint
