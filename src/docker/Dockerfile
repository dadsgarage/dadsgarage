FROM bitnami/minideb:buster

LABEL name="dadsgarage"

# Make all shells run in a safer way. Ref: https://vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/
SHELL [ "/bin/bash", "-euxo", "pipefail", "-c" ]

WORKDIR /

# Need root to do rooty things
USER root

# Install debian apt-get packages
# Some of this is lifted from bitnami/minideb-extras
RUN install_packages \
  apt-transport-https \
  build-essential \
  ca-certificates \
  curl \
  dirmngr \
  git \
  gnupg \
  gosu \
  jq \
  libaio1 \
  locales \
  openssh-client \
  pkg-config \
  procps \
  unzip

# Update the locale for container-optimized settings, and add a user so we aren't stuck with using root.
RUN update-locale LANG=C.UTF-8 LC_MESSAGES=POSIX \
  && locale-gen en_US.UTF-8 \
  && DEBIAN_FRONTEND=noninteractive dpkg-reconfigure locales \
  && useradd -ms /bin/bash dadsgarage

# The following security actions are recommended by some security scans.
# https://console.bluemix.net/docs/services/va/va_index.html
# This is lifted straight out of bitnami/minideb-extras
RUN  sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS    90/' /etc/login.defs \
  && sed -i 's/^PASS_MIN_DAYS.*/PASS_MIN_DAYS    1/' /etc/login.defs \
  && sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Add Tini. Ref: https://github.com/krallin/tini
ARG TINI_VERSION=v0.18.0
RUN curl -sOL https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini && \
    chmod +x /tini

# Install kubectl
ARG KUBECTL_VERSION=1.16.3-00
RUN curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - && \
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | tee -a /etc/apt/sources.list.d/kubernetes.list && \
  install_packages kubectl=${KUBECTL_VERSION}

ARG K9S_VERSION=0.9.3
RUN curl -LO https://github.com/derailed/k9s/releases/download/${K9S_VERSION}/k9s_${K9S_VERSION}_Linux_x86_64.tar.gz && \
  tar -C /usr/local/bin -xzvf k9s_${K9S_VERSION}_Linux_x86_64.tar.gz k9s && \
  chmod +x /usr/local/bin/k9s && \
  rm -rf k9s_${K9S_VERSION}_Linux_x86_64.tar.gz

# Install Helm 2
ARG HELM2_VERSION=v2.16.1
RUN curl -LO https://get.helm.sh/helm-${HELM2_VERSION}-linux-amd64.tar.gz && \
    tar -zxvf helm-${HELM2_VERSION}-linux-amd64.tar.gz && \
    mv linux-amd64/helm /usr/local/bin/helm2 && \
    chmod +x /usr/local/bin/helm2 && \
    rm -rf linux-amd64 helm-${HELM2_VERSION}-linux-amd64.tar.gz

# Install Helm 3
ARG HELM3_VERSION=v3.0.2
RUN curl -LO https://get.helm.sh/helm-${HELM3_VERSION}-linux-amd64.tar.gz && \
    tar -zxvf helm-${HELM3_VERSION}-linux-amd64.tar.gz && \
    mv linux-amd64/helm /usr/local/bin/helm && \
    chmod +x /usr/local/bin/helm && \
    rm -rf linux-amd64 helm-${HELM3_VERSION}-linux-amd64.tar.gz

# Install helmfile
ARG HELMFILE_VERSION=v0.97.0
RUN curl -sOL https://github.com/roboll/helmfile/releases/download/${HELMFILE_VERSION}/helmfile_linux_amd64 && \
    chmod +x ./helmfile_linux_amd64 && \
    mv helmfile_linux_amd64 /usr/local/bin/helmfile

# Install Terraform
ARG TERRAFORM_VERSION=0.12.19
RUN curl -sOL https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip && \
    unzip terraform_${TERRAFORM_VERSION}_linux_amd64.zip -d /usr/local/bin/ && \
    rm -f terraform_${TERRAFORM_VERSION}_linux_amd64.zip

# Install Azure cli
ARG AZURE_CLI=2.0.78-1~buster
RUN curl -sL https://packages.microsoft.com/keys/microsoft.asc | \
    gpg --dearmor > /etc/apt/trusted.gpg.d/microsoft.asc.gpg && \
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ buster main" | \
    tee -a /etc/apt/sources.list.d/azure-cli.list && \
    install_packages azure-cli=${AZURE_CLI}

# Quit being root for safety.
USER dadsgarage

# Install asdf. Get versions from https://github.com/asdf-vm/asdf/releases
ARG ASDF_VERSION="0.7.7"
ENV ASDF_VERSION=${ASDF_VERSION}
# hadolint ignore=SC2016
RUN git clone --branch "v${ASDF_VERSION}" --depth 1 https://github.com/asdf-vm/asdf.git "${HOME}/.asdf" \
  && echo -e '\nsource $HOME/.asdf/asdf.sh' >> "${HOME}/.bashrc" \
  && echo -e '\nsource $HOME/.asdf/asdf.sh' >> "${HOME}/.profile" \
  && source "${HOME}/.asdf/asdf.sh" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

ENV PATH="/home/dadsgarage/.asdf/shims:/home/dadsgarage/.asdf/bin:${PATH}"

# Install python. Get versions using 'asdf list all python'
ARG PYTHON_VERSION="3.8.2"
ENV PYTHON_VERSION=${PYTHON_VERSION}
RUN asdf plugin add python \
  && asdf install python "${PYTHON_VERSION}" \
  && asdf global python "${PYTHON_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install pre-commit. Get versions using 'pip install pre-commit=='
ARG PRE_COMMIT_VERSION="2.1.1"
ENV PRE_COMMIT_VERSION=${PRE_COMMIT_VERSION}
RUN pip install "pre-commit==${PRE_COMMIT_VERSION}" \
  && asdf reshim python \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install aws cli. Get versions using pip install awscli=='
ARG AWS_CLI_VERSION="1.18.24"
ENV AWS_CLI_VERSION=${AWS_CLI_VERSION}
RUN pip install "awscli==${AWS_CLI_VERSION}" \
  && asdf reshim python \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# helm 2 setup
ARG HELMDIFF_VERSION=v3.0.0-rc.7
RUN helm2 init --client-only && \
    helm2 plugin install https://github.com/databus23/helm-diff --version ${HELMDIFF_VERSION} && \
    alias helmfile2="helmfile --helm-binary helm2"

# helm 3 setup
RUN helm plugin install https://github.com/databus23/helm-diff --version ${HELMDIFF_VERSION}

WORKDIR /home/dadsgarage

ENTRYPOINT ["/tini", "--"]
