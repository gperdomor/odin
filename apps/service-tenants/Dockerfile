FROM node:14.15-alpine as development
WORKDIR /app
COPY package*.json ./
RUN CYPRESS_INSTALL_BINARY=0 npm install --quiet
COPY . .
RUN npm run nx run service-tenants:build -- --c=production

FROM node:14.15-alpine as cleaner
WORKDIR /app
COPY package*.json ./
COPY --chown=node:node --from=development /app/node_modules ./node_modules
RUN npm prune --production

FROM node:14.15-alpine as production
LABEL maintainer="Gustavo Perdomo <gperdomor@gmail.com>"
ARG NODE_ENV=production
ENV NODE_ENV $NODE_ENV
RUN apk add --no-cache bash=5.0.11-r1
WORKDIR /app
USER node
COPY --chown=node:node package*.json ./
COPY --chown=node:node --from=cleaner /app/node_modules ./node_modules
COPY --chown=node:node --from=development /app/dist/apps/service-tenants .
# COPY --chown=node:node ./tools/scripts/entrypoint/service-tenants.sh /usr/local/bin/docker-entrypoint.sh
# ENTRYPOINT [ "docker-entrypoint.sh" ]
CMD ["node", "main"]
