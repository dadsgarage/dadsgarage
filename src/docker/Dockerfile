FROM bitnami/minideb:buster

# Make all shells run in a safer way. Ref: https://vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/
SHELL [ "/bin/bash", "-euxo", "pipefail", "-c" ]
WORKDIR /

# Need root to do rooty things
USER root

# Install a bunch of packages, update the locale for container-optimized settings, and add a user so we aren't stuck with using root.
# Some of this is lifted from bitnami/minideb-extras
RUN install_packages \
  build-essential \
  ca-certificates \
  curl \
  dirmngr \
  git \
  gnupg \
  gosu \
  libaio1 \
  locales \
  pkg-config \
  procps \
  python3 \
  python3-pip \
  python3-setuptools \
  python3-venv \
  unzip \
  && update-locale LANG=C.UTF-8 LC_MESSAGES=POSIX \
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
ENV TINI_VERSION v0.18.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini

# GitHub Actions changes the $HOME environment variable to /github/home, so set up a symlink
RUN mkdir -p /github/home && ln -s /home/dadsgarage /github/home

# Quit being root for safety.
USER dadsgarage

# Install pipx for safer pip installations of tools. Ref: https://pipxproject.github.io/pipx/
ENV PATH="${PATH}:~/.local/bin"
RUN python3 -m pip install --user pipx \
  && pipx ensurepath

# Install pre-commit
ENV PRE_COMMIT_VERSION=1.20.0
RUN pipx install pre-commit==${PRE_COMMIT_VERSION}

ENTRYPOINT ["/tini", "--"]
