# syntax=docker/dockerfile:1.7

FROM node:26-bookworm-slim@sha256:5df79989ed685e09f0a98019db4cebfb22fb9df1a53c0161664a011f7ff6b0f1

ENV COREPACK_ENABLE_DOWNLOAD_PROMPT=0

RUN --mount=type=cache,id=openclaw-cleanup-smoke-apt-cache,target=/var/cache/apt,sharing=locked \
  --mount=type=cache,id=openclaw-cleanup-smoke-apt-lists,target=/var/lib/apt,sharing=locked \
  apt-get update \
  && DEBIAN_FRONTEND=noninteractive apt-get upgrade -y --no-install-recommends \
  && apt-get install -y --no-install-recommends \
    bash \
    ca-certificates \
    git

WORKDIR /repo
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./
COPY ui/package.json ./ui/package.json
COPY packages ./packages
COPY extensions ./extensions
COPY patches ./patches
RUN --mount=type=cache,id=openclaw-pnpm-store,target=/root/.local/share/pnpm/store,sharing=locked \
  corepack enable \
  && pnpm install --frozen-lockfile

COPY . .
COPY --chmod=755 scripts/docker/cleanup-smoke/run.sh /usr/local/bin/openclaw-cleanup-smoke

ENTRYPOINT ["/usr/local/bin/openclaw-cleanup-smoke"]
