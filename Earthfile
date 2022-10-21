VERSION 0.6
FROM DOCKERFILE .
WORKDIR /workspace
COPY aqua.yaml .
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

tf-fmt:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    WORKDIR /workspace/$dir
    RUN terraform fmt -check

tf-plan:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    COPY +tf-init/.terraform $dir/.terraform
    COPY +tf-init/.terraform.lock.hcl $dir/.terraform.lock.hcl
    WORKDIR /workspace/$dir
    RUN terraform plan -input=false

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
