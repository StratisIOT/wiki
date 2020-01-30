# ====================
# --- Build Assets ---
# ====================
FROM node:12.14-alpine AS assets

RUN apk add yarn g++ make python --no-cache

WORKDIR /wiki

ARG NPM_TOKEN
COPY ./client ./client
COPY ./dev ./dev
COPY ./package.json ./package.json
COPY ./.babelrc ./.babelrc
COPY ./.eslintignore ./.eslintignore
COPY ./.eslintrc.yml ./.eslintrc.yml


# RUN yarn cache clean
# RUN yarn --frozen-lockfile --non-interactive
# RUN yarn build
# RUN rm -rf /wiki/node_modules
# RUN yarn --production --frozen-lockfile --non-interactive
# COPY ./node_modules ./node_modules

ARG NPM_TOKEN

COPY ./.npmrc ./.npmrc
RUN npm install
RUN npm run build
RUN rm -rf /wiki/node_modules
RUN npm --only=production install
RUN rm -f .npmrc

# ===============
# --- Release ---
# ===============
FROM node:12.14-alpine
LABEL maintainer="stratisiot.com"

RUN apk add bash curl git openssh gnupg sqlite --no-cache && \
    mkdir -p /wiki && \
    mkdir -p /logs && \
    chown -R node:node /wiki /logs

WORKDIR /wiki

COPY --chown=node:node ./assets ./assets
COPY --chown=node:node ./node_modules ./node_modules
COPY --chown=node:node ./server ./server
COPY --chown=node:node ./server/views ./server/views
COPY --chown=node:node ./dev/build/config.yml ./config.yml
COPY --chown=node:node ./package.json ./package.json
COPY --chown=node:node ./LICENSE ./LICENSE

USER node

EXPOSE 3000

# HEALTHCHECK --interval=30s --timeout=30s --start-period=30s --retries=3 CMD curl -f http://localhost/healthz

CMD ["node", "server"]
