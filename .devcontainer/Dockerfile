# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.183.0/containers/go/.devcontainer/base.Dockerfile

# [Choice] Go version: 1, 1.16, 1.15
ARG GO_VERSION="1.16"
FROM mcr.microsoft.com/vscode/devcontainers/go:0-${GO_VERSION}


# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>

# Install Docker CE CLI
RUN apt-get update \
    && apt-get install -y apt-transport-https ca-certificates curl gnupg2 lsb-release \
    && curl -fsSL https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/gpg | apt-key add - 2>/dev/null \
    && echo "deb [arch=amd64] https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]') $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list \
    && apt-get update \
    && apt-get install -y docker-ce-cli

# Install Docker Compose
RUN LATEST_COMPOSE_VERSION=$(curl -sSL "https://api.github.com/repos/docker/compose/releases/latest" | grep -o -P '(?<="tag_name": ").+(?=")') \
    && curl -sSL "https://github.com/docker/compose/releases/download/${LATEST_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose \
    && chmod +x /usr/local/bin/docker-compose

# Install kubectl
RUN curl -sSL -o /usr/local/bin/kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl \
    && chmod +x /usr/local/bin/kubectl

# Install Helm
RUN curl -s https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash -


ARG OPERATOR_SDK_VERSION="v1.9.0"
RUN export ARCH=$(case $(uname -m) in x86_64) echo -n amd64 ;; aarch64) echo -n arm64 ;; *) echo -n $(uname -m) ;; esac) \ 
    && export OS=$(uname | awk '{print tolower($0)}') \
    && export OPERATOR_SDK_DL_URL=https://github.com/operator-framework/operator-sdk/releases/download/${OPERATOR_SDK_VERSION} \
    && curl -LO ${OPERATOR_SDK_DL_URL}/operator-sdk_${OS}_${ARCH} \ 
    && gpg --keyserver keyserver.ubuntu.com --recv-keys 052996E2A20B5C7E \
    && curl -LO ${OPERATOR_SDK_DL_URL}/checksums.txt \
    && curl -LO ${OPERATOR_SDK_DL_URL}/checksums.txt.asc \
    && gpg -u "Operator SDK (release) <cncf-operator-sdk@cncf.io>" --verify checksums.txt.asc \
    && grep operator-sdk_${OS}_${ARCH} checksums.txt | sha256sum -c - \
    && chmod +x operator-sdk_${OS}_${ARCH} && mv operator-sdk_${OS}_${ARCH} /usr/local/bin/operator-sdk 

COPY copy-kube-config.sh /usr/local/share/
RUN echo "source /usr/local/share/copy-kube-config.sh" | tee -a /home/vscode/.bashrc >> /home/vscode/.zshrc