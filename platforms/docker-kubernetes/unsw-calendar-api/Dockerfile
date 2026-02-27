
#build
FROM node:20-bookworm-slim AS builder

WORKDIR /unsw-calendar-api

COPY package.json ./
RUN npm install

COPY . .
RUN npm run build

#production runtime

FROM node:20-bookworm-slim

WORKDIR /unsw-calendar-api

#environment defaults to production settings
ENV NODE_ENV=production;

#install only production deps and not devdeps
COPY package.json ./
RUN npm install --omit=dev

COPY --from=builder /unsw-calendar-api/dist ./dist

EXPOSE 3000

CMD ["node", "dist/server.js"]