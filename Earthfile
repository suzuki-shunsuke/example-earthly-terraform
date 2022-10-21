VERSION 0.6
IMPORT ./lib AS lib
FROM DOCKERFILE .
WORKDIR /workspace
COPY aqua.yaml .
RUN aqua i -l
ARG ci

validate-arg-dir:
    ARG dir=.
    DO lib+VALIDATE_ARG_DIR -dir $dir

tf-init:
    ARG dir=.
    COPY $dir $dir
    DO lib+TF_INIT -dir=$dir 

tf-validate:
    ARG dir=.
    DO lib+VALIDATE_ARG_DIR -dir $dir
    COPY $dir $dir
    COPY +tf-init/.terraform $dir/.terraform
    COPY +tf-init/.terraform.lock.hcl $dir/.terraform.lock.hcl
    WORKDIR /workspace/$dir
    DO lib+TF_VALIDATE -dir=$dir 

tf-fmt:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    WORKDIR /workspace/$dir
    DO lib+TF_FMT -dir=$dir 

tf-plan:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    COPY +tf-init/.terraform $dir/.terraform
    COPY +tf-init/.terraform.lock.hcl $dir/.terraform.lock.hcl
    WORKDIR /workspace/$dir
    DO lib+TF_PLAN -dir=$dir 

conftest:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY policy policy
    COPY +tf-plan/tfplan.json .
    DO lib+CONFTEST -dir=$dir 

tfsec:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    WORKDIR /workspace/$dir
    DO lib+TFSEC -dir=$dir 

tflint:
    ARG dir=.
    FROM +validate-arg-dir -dir=$dir 
    COPY $dir $dir
    WORKDIR /workspace/$dir
    DO lib+TFLINT -dir=$dir 
