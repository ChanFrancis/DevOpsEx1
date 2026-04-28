FROM dhi.io/node:24-alpine3.23 AS build

WORKDIR /app

COPY package*.json .

RUN npm install

COPY . .

RUN npm run build

FROM dhi.io/node:24-alpine3.23 AS prod

WORKDIR /app

COPY --from=build /app/.output /app/.output

ENV NODE_ENV=production

EXPOSE 3000

CMD [ "node", ".output/server/index.mjs" ]
