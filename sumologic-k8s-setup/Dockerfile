FROM registry.access.redhat.com/ubi8/ubi

ENV TERRAFORM_VERSION=0.13.7
ARG TARGETPLATFORM

RUN yum update -y
RUN yum install unzip -y
RUN if [ "${TARGETPLATFORM}" = "linux/amd64" ]; then TERRAFORM_PLATFORM="linux_amd64"; fi; \
    if [ "${TARGETPLATFORM}" = "linux/arm/v7" ]; then TERRAFORM_PLATFORM="linux_arm"; fi; \
    if [ "${TARGETPLATFORM}" = "linux/arm64" ]; then TERRAFORM_PLATFORM="linux_arm64"; fi; \
    if [ "${TERRAFORM_PLATFORM}" = "" ]; then TERRAFORM_PLATFORM="${TARGETPLATFORM}"; fi \
 && curl -o terraform.zip https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_${TERRAFORM_PLATFORM}.zip \
 && unzip terraform.zip \
 && mv terraform /usr/local/bin/ \
 && rm terraform.zip \
 # ping group has a conflicting id: 999 so delete it
 && groupadd -g 993 setup \
 && useradd -u 933 setup -g setup \
 && mkdir /terraform /scripts \
 && chown -R setup:setup /terraform /scripts

USER setup
RUN cd /terraform/ \
 && curl -O https://raw.githubusercontent.com/SumoLogic/sumologic-kubernetes-collection/7aeb5014aad04c21a21fea2d88e35600da6818eb/deploy/helm/sumologic/conf/setup/main.tf \
 && terraform init \
 && rm main.tf

ARG BUILD_TAG=latest
ENV TAG=$BUILD_TAG

WORKDIR /terraform/
