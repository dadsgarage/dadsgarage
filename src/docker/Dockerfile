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
  libbz2-dev \
  libffi-dev \
  liblzma-dev \
  libncurses5-dev \
  libncursesw5-dev \
  libreadline-dev \
  libsqlite3-dev \
  libssl-dev \
  llvm \
  locales \
  make \
  openssh-client \
  pkg-config \
  python-openssl \
  procps \
  tk-dev \
  unzip \
  wget \
  xz-utils \
  zlib1g-dev

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

# Install helmfile
ARG HELMFILE_VERSION=v0.97.0
RUN curl -sOL https://github.com/roboll/helmfile/releases/download/${HELMFILE_VERSION}/helmfile_linux_amd64 && \
    chmod +x ./helmfile_linux_amd64 && \
    mv helmfile_linux_amd64 /usr/local/bin/helmfile

# Install Azure cli
ARG AZURE_CLI=2.0.78-1~buster
RUN curl -sL https://packages.microsoft.com/keys/microsoft.asc | \
    gpg --dearmor > /etc/apt/trusted.gpg.d/microsoft.asc.gpg && \
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ buster main" | \
    tee -a /etc/apt/sources.list.d/azure-cli.list && \
    install_packages azure-cli=${AZURE_CLI}

# Install go-task. Get versions from https://github.com/go-task/task/releases
ARG GO_TASK_VERSION="2.8.1"
# hadolint ignore=DL3003
RUN curl -sL -o /root/task.tar.gz "https://github.com/go-task/task/releases/download/v${GO_TASK_VERSION}/task_linux_amd64.tar.gz" \
  && (cd /usr/local/bin && tar -xzvf /root/task.tar.gz task) \
  && chmod +x /usr/local/bin/task \
  && rm -f /root/task.tar.gz \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Quit being root for safety.
USER dadsgarage

# Install asdf. Get versions from https://github.com/asdf-vm/asdf/releases
ARG ASDF_VERSION="0.8.0"
# hadolint ignore=SC2016
RUN git clone --branch "v${ASDF_VERSION}" --depth 1 https://github.com/asdf-vm/asdf.git "${HOME}/.asdf" \
  && echo -e '\nsource $HOME/.asdf/asdf.sh' >> "${HOME}/.bashrc" \
  && echo -e '\nsource $HOME/.asdf/asdf.sh' >> "${HOME}/.profile" \
  && source "${HOME}/.asdf/asdf.sh" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

ENV PATH="/home/dadsgarage/.asdf/shims:/home/dadsgarage/.asdf/bin:${PATH}"

# Install python. Get versions using 'asdf list all python'
ARG PYTHON_VERSION="3.8.2"
RUN asdf plugin add python \
  && asdf install python "${PYTHON_VERSION}" \
  && asdf global python "${PYTHON_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install nodejs. Get versions using 'asdf list all nodejs'
ARG NODEJS_VERSION="12.16.1"
RUN asdf plugin add nodejs \
  && bash ~/.asdf/plugins/nodejs/bin/import-release-team-keyring \
  && asdf install nodejs "${NODEJS_VERSION}" \
  && asdf global nodejs "${NODEJS_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install kubectl. Get versions using 'asdf list all kubectl'
ARG KUBECTL_VERSION="1.17.4"
RUN asdf plugin add kubectl \
  && asdf install kubectl "${KUBECTL_VERSION}" \
  && asdf global kubectl "${KUBECTL_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install Helm. Get versions using 'asdf list all helm'.
ARG HELM_VERSION="3.1.2"
RUN asdf plugin add helm \
  && asdf install helm "${HELM_VERSION}" \
  && asdf global helm "${HELM_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install helm-diff plugin. Get versions from https://github.com/databus23/helm-diff/releases
ARG HELM_DIFF_VERSION="3.1.1"
RUN helm plugin install https://github.com/databus23/helm-diff --version "${HELM_DIFF_VERSION}" \
 && asdf reshim helm \
 && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install helm-git plugin. Get versions from https://github.com/aslafy-z/helm-git/releases
ARG HELM_GIT_VERSION="0.6.0"
RUN helm plugin install https://github.com/aslafy-z/helm-git.git --version "${HELM_GIT_VERSION}" \
  && asdf reshim helm \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install pre-commit. Get versions using 'pip install pre-commit=='
ARG PRE_COMMIT_VERSION="2.7.1"
RUN pip install "pre-commit==${PRE_COMMIT_VERSION}" \
  && asdf reshim python \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install aws cli. Get versions using pip install awscli=='
ARG AWS_CLI_VERSION="1.18.24"
RUN pip install "awscli==${AWS_CLI_VERSION}" \
  && asdf reshim python \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install terraform. Get versions using 'asdf list all terraform'.
ARG TERRAFORM_VERSION="0.12.24"
RUN asdf plugin add terraform \
  && asdf install terraform "${TERRAFORM_VERSION}" \
  && asdf global terraform "${TERRAFORM_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install terraform-docs. Get versions using 'asdf list all terraform-docs'.
ARG TERRAFORM_DOCS_VERSION="v0.9.1"
RUN asdf plugin add terraform-docs \
  && asdf install terraform-docs "${TERRAFORM_DOCS_VERSION}" \
  && asdf global terraform-docs "${TERRAFORM_DOCS_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install terraform-docs. Get versions using 'asdf list all tflint'.
ARG TFLINT_VERSION="0.15.4"
RUN asdf plugin add tflint \
  && asdf install tflint "${TFLINT_VERSION}" \
  && asdf global tflint "${TFLINT_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install tfsec. Get versions using 'asdf list all tfsec'.
ARG TFSEC_VERSION="0.30.1"
RUN asdf plugin add tfsec \
  && asdf install tfsec "${TFSEC_VERSION}" \
  && asdf global tfsec "${TFSEC_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Install k9s. Get versions using 'asdf list all k9s'.
ARG K9S_VERSION="0.19.1"
RUN asdf plugin add k9s \
  && asdf install k9s "${K9S_VERSION}" \
  && asdf global k9s "${K9S_VERSION}" \
  && rm -rf /var/tmp/* /tmp/* /var/tmp/.???* /tmp/.???*

# Support asdf when running as root user
USER root
ENV ASDF_DATA_DIR="/home/dadsgarage/.asdf"
RUN cp /home/dadsgarage/.tool-versions /root/.tool-versions
USER dadsgarage

WORKDIR /home/dadsgarage

ENTRYPOINT ["/tini", "--"]
