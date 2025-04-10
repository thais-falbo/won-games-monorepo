ARG APP=strapi
ARG WORKSPACE=@repo/strapi

FROM node:22-alpine AS base
# RUN corepack enable && corepack install -g yarn@4.7.0
# RUN yarn set version berry
RUN apk update && apk add --no-cache libc6-compat

### PRUNED ###
FROM base AS pruned
ARG WORKSPACE

# Set working directory
WORKDIR /app
COPY . .

RUN yarn global add turbo@^2.4.4
RUN turbo prune ${WORKSPACE} --docker

### INSTALLER ###
FROM base AS installer
ARG APP
ARG WORKSPACE
ARG NODE_ENV=development

WORKDIR /app

# Strapi dependencies
RUN apk update && apk add --no-cache build-base gcc autoconf automake zlib-dev libpng-dev vips-dev git > /dev/null 2>&1

COPY .gitignore .gitignore
COPY --from=pruned /app/out/json/ .
COPY --from=pruned /app/out/yarn.lock ./yarn.lock

RUN yarn global add node-gyp
RUN yarn global add turbo@^2.4.4

RUN \
    --mount=type=cache,target=/usr/local/share/.cache/yarn/v6,sharing=locked \
    yarn --prefer-offline --frozen-lockfile --ignore-scripts --production

RUN \
    --mount=type=cache,target=/usr/local/share/.cache/yarn/v6,sharing=locked \
    yarn workspace ${WORKSPACE} add sharp --ignore-engines --prefer-offline --frozen-lockfile

ENV PATH=/app/apps/${APP}/node_modules/.bin:$PATH

COPY --from=pruned /app/out/full/ .
COPY ../turbo.json turbo.json

RUN turbo run build --filter=${WORKSPACE}

### RUNNER ###
FROM base AS runner
ARG APP
WORKDIR /app
ARG NODE_ENV=development

RUN apk update && apk add --no-cache vips-dev

RUN addgroup --system --gid 1001 strapi
RUN adduser --system --uid 1001 strapi

WORKDIR /app
COPY --from=installer /app .
RUN chown -R strapi:strapi .
USER strapi

ENV PATH=/app/apps/${APP}/node_modules/.bin:$PATH
WORKDIR /app/apps/${APP}
EXPOSE ${PORT:-1337}
CMD ["yarn", "develop"]
