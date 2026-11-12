FROM node:20

RUN apt-get update && apt-get install -y --no-install-recommends \
    git curl less procps sudo zsh jq ca-certificates \
    && rm -rf /var/lib/apt/lists/*

ENV NPM_CONFIG_PREFIX=/usr/local/share/npm-global
ENV PATH=$PATH:/usr/local/share/npm-global/bin

RUN mkdir -p /usr/local/share/npm-global /workspace /home/node/.claude \
    && chown -R node:node /usr/local/share/npm-global /workspace /home/node/.claude

USER node
WORKDIR /workspace

RUN npm install -g @anthropic-ai/claude-code

CMD ["sleep", "infinity"]