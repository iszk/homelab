FROM debian:trixie

ENV TZ=Asia/Tokyo
ENV LANG=C.UTF-8

ARG HOST_UID=1000
ARG HOST_GID=1000
ARG HOST_DOCKER_GID=989
ARG USER_NAME=vscode
ARG SRC_DIR=/src

RUN apt-get update && apt-get install \
    apt-transport-https \
    ca-certificates \
    openssh-client \
    libgomp1 \
    procps \
    gnupg \
    fd-find \
    sudo \
    vim \
    curl \
    zsh \
    git \
    ripgrep \
    just \
    fzf \
    -y --no-install-recommends

# vscode
RUN curl -L https://go.microsoft.com/fwlink/?LinkID=760868 -o /tmp/vscode.deb
RUN apt-get install -y --no-install-recommends /tmp/vscode.deb
RUN rm /tmp/vscode.deb

# docker cli
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg && chmod a+r /etc/apt/keyrings/docker.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian trixie stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
RUN apt-get update && apt-get install \
    docker-ce-cli \
    docker-compose-plugin \
    -y --no-install-recommends

RUN groupadd -g ${HOST_GID} ${USER_NAME}
RUN useradd -m -u ${HOST_UID} -g ${HOST_GID} -s /usr/bin/zsh ${USER_NAME}
RUN echo "${USER_NAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/${USER_NAME}
RUN ln -s /usr/bin/fdfind /usr/local/bin/fd
RUN groupadd -g ${HOST_DOCKER_GID} docker
RUN usermod -aG ${HOST_DOCKER_GID} ${USER_NAME}

# TODO docker group に所属させる
# 未チェックなので確認して対応する
# RUN usermod -aG 989 ${USER_NAME}
# 989 は docker グループの GID で、DooD で docker compose などを利用するために必要

# DooD で docker compose などを利用するため、ディレクトリ名をホスト側と合わせる
RUN mkdir -p ${SRC_DIR}
RUN chown -R ${USER_NAME}:${USER_NAME} ${SRC_DIR}
WORKDIR ${SRC_DIR}
ENV GHQ_ROOT=${SRC_DIR}

RUN curl https://mise.run | MISE_INSTALL_PATH="/usr/local/bin/mise" sh

USER ${USER_NAME}

RUN mkdir -p ~/.ssh && chmod 700 ~/.ssh
RUN mkdir -p ~/.local/bin
RUN mkdir -p ~/.local/share/mise/installs
RUN mkdir -p ~/.config
RUN mkdir -p ~/.vscode
RUN mkdir -p ~/.vscode-server
RUN mise use --global node@24
RUN mise use --global ghq@latest

RUN echo 'eval "$(mise activate bash)"' >> ~/.bashrc

RUN ln -s /opt/equuss/src/github.com/iszk/workspaces/vscode ~/workspaces

ENV PORT=8000
EXPOSE ${PORT}
CMD ["code", "serve-web", "--host", "0.0.0.0", "--port", "8000", "--without-connection-token", "--accept-server-license-terms"]
