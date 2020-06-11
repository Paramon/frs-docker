FROM node:10-alpine AS builder

ARG REGISTRY_URL
ARG REGISTRY_AUTH_TOKEN

RUN apk --no-cache upgrade && \
    apk --no-cache add alpine-sdk python2 libtool autoconf automake postgresql-dev libsodium gcc g++

RUN addgroup -g 1100 frs && \
    adduser -h /home/frs -s /bin/bash -u 1100 -G frs -D frs
COPY --chown=frs:frs . /home/frs/frs/
RUN if [ -n "$REGISTRY_URL" ]; then \
      echo -e "registry=$REGISTRY_URL/\n${REGISTRY_URL#*:}/:_authToken=$REGISTRY_AUTH_TOKEN" >/home/frs/.npmrc; \
    fi

RUN npm install node-gyp -g
USER frs
WORKDIR /home/frs/frs

RUN npm i libpq
RUN npm i sodium --unsafe-perm

RUN npm i 

FROM node:10-alpine

ENV NODE_ENV=production
ENV WFI_COMMIT=e34c502a3efe0e8b8166ea6148d55b73da5c8401
ENV WFI_SHA=0f75de5c9d9c37a933bb9744ffd710750d5773892930cfe40509fa505788835c

RUN apk --no-cache upgrade && \
    apk --no-cache add bash curl jq python2-dev postgresql-dev

RUN addgroup -g 1100 frs && \
    adduser -h /home/frs -s /bin/bash -u 1100 -G frs -D frs
COPY --from=builder --chown=frs:frs /home/frs/frs/ /home/frs/frs/
RUN mkdir -p /home/frs/frs/logs && \
    chown frs:frs /home/frs/frs/logs


WORKDIR /home/frs/frs

ENTRYPOINT ["node", "/home/frs/frs/app.js"]