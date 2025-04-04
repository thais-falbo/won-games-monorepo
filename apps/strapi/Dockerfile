ARG APP=strapi
ARG WORKSPACE=strapi

FROM node:22-alpine AS base
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable
RUN corepack install -g pnpm@latest-10

RUN apk update && apk add --no-cache libc6-compat

### PRUNED ###
FROM base AS pruned
ARG WORKSPACE

# Set working directory
WORKDIR /app
COPY . .

RUN pnpm install -g turbo@^2.4.4
RUN turbo prune ${WORKSPACE} --docker

### INSTALLER ###
FROM base AS installer
ARG APP
ARG WORKSPACE
ARG NODE_ENV=development

WORKDIR /app

# Strapi dependencies
RUN apk update && apk add --no-cache build-base gcc autoconf automake zlib-dev libpng-dev nasm bash vips-dev git

COPY .gitignore .gitignore
COPY --from=pruned /app/out/json/ .
COPY --from=pruned /app/out/pnpm-lock.yaml /app/pnpm-lock.yaml

RUN pnpm install -g node-gyp
RUN pnpm install -g turbo@^2.4.4

RUN \
    --mount=type=cache,id=pnpm,target=/pnpm/store \
    pnpm install --frozen-lockfile

RUN \
    --mount=type=cache,id=pnpm,target=/pnpm/store \
    pnpm install sharp --prefer-offline -w=${WORKSPACE}

ENV PATH=/app/apps/${APP}/node_modules/.bin:$PATH

COPY --from=pruned /app/out/full/ .
COPY turbo.json turbo.json

RUN pnpm turbo run build --filter=${WORKSPACE}

### RUNNER ###
FROM base AS runner
ARG APP
WORKDIR /app

RUN addgroup --system --gid 1001 strapi
RUN adduser --system --uid 1001 strapi
USER strapi

WORKDIR /app
COPY --from=installer /app .

ENV PATH=/app/apps/${APP}/node_modules/.bin:$PATH

WORKDIR /app/apps/${APP}
EXPOSE ${PORT:-1337}
CMD ["pnpm", "start"]
