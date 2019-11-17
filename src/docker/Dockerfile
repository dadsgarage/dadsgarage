FROM bitnami/minideb-extras:latest-buildpack

# Make all shells run in a safer way. Ref: https://vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/
SHELL [ "/bin/bash", "-euxo", "pipefail", "-c" ]

# Need root to install system packages
USER root

# Install Python3 and Pip
RUN install_packages \
  python3 \
  python3-pip \
  python3-setuptools

# Quit being root for safety.
USER bitnami

# Install pipx for safer pip installations of tools. Ref: https://pipxproject.github.io/pipx/
RUN python3 -m pip install --user pipx \
  && pipx ensurepath
