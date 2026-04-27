# TODO: remplacer par l'image DHI Node trouvée sur https://dhi.io (ex: docker.io/dhi/node:24)
FROM node:24-alpine AS build

WORKDIR /app

COPY package*.json .

RUN npm install

COPY . .

RUN npm run build

# TODO: remplacer par l'image DHI Node trouvée sur https://dhi.io (ex: docker.io/dhi/node:24)
FROM node:24-alpine AS prod

WORKDIR /app

COPY --from=build /app/.output /app/.output

ENV NODE_ENV=production

EXPOSE 3000

CMD [ "node", ".output/server/index.mjs" ]
