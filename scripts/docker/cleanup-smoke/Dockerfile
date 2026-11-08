# syntax=docker/dockerfile:1.7

FROM node:25-bookworm-slim@sha256:81db02c4b671288a03915da9534dbd54f96d0e7c24d80ccc54f5b36b2e684370

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
