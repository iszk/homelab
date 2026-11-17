FROM node:24-trixie-slim

ENV TZ=Asia/Tokyo
ENV LANG=C.UTF-8

ARG USER_NAME=node

RUN apt-get update && apt-get install \
    apt-transport-https \
    ca-certificates \
    wget \
    -y --no-install-recommends

RUN mkdir -p -m 755 /etc/apt/keyrings; \
    wget -nv -O /etc/apt/keyrings/githubcli-archive-keyring.gpg \
    https://cli.github.com/packages/githubcli-archive-keyring.gpg; \
    chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg; \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
    > /etc/apt/sources.list.d/github-cli.list

RUN apt-get update && apt-get install \
    libsecret-1-0 \
    bubblewrap \
    openssh-client \
    libgomp1 \
    gnupg \
    curl \
    git \
    ripgrep \
    fd-find \
    procps \
    just \
    gh \
    -y --no-install-recommends

RUN ln -s /usr/bin/fdfind /usr/local/bin/fd

RUN curl https://mise.run | MISE_INSTALL_PATH="/usr/local/bin/mise" sh

USER ${USER_NAME}
WORKDIR /home/${USER_NAME}
ENV NPM_CONFIG_PREFIX=/home/node/.npm-global
ENV PATH=/home/node/bin:$PATH:/home/node/.npm-global/bin:/home/node/.local/bin

RUN echo 'eval "$(mise activate bash)"' >> ~/.bashrc
# RUN echo 'export PATH="$HOME/bin:$HOME/.local/bin:$PATH"' >> ~/.bashrc

RUN mkdir -p .local/bin
RUN mkdir -p .config
RUN mkdir -p .cache
RUN mkdir -p .codex
RUN mkdir -p .copilot
RUN mkdir -p .gemini
RUN mkdir -p .cursor
RUN mkdir -p .agents/skills
RUN mkdir -p .ssh
# RUN mkdir -p .local/share/mise/installs

RUN npm install -g @openai/codex@latest
RUN npm exec --prefer-online --package=@agentclientprotocol/codex-acp@latest -- true
# RUN npm install -g opencode-ai@latest
# RUN curl https://cursor.com/install -fsS | bash

CMD ["/usr/local/bin/npx", "--yes", "kandev"]
