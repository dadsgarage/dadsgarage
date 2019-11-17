FROM bitnami/minideb-extras:latest-buildpack

# Make all shells run in a safer way. Ref: https://vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/
SHELL [ "/bin/bash", "-euxo", "pipefail", "-c" ]

# Need root to install system packages
USER root

# Install Python3 and Pip
RUN install_packages \
  python3 \
  python3-pip

# Quit being root for safety. Ref: https://medium.com/@mccode/processes-in-containers-should-not-run-as-root-2feae3f0df3b
RUN groupadd -g 999 dadsgarage \
  && useradd -r -u 999 -g dadsgarage dadsgarage
USER dadsgarage

# Install pipx for safer pip installations of tools. Ref: https://pipxproject.github.io/pipx/
RUN python3 -m pip install --user pipx \
  && python3 -m pipx ensurepath
