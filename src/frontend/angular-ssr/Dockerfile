# syntax=docker/dockerfile:1.4

FROM node:lts-alpine AS development
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
ENV CI=true
RUN npm cache clean --force

FROM development AS build
RUN npm run build

FROM node:lts-alpine
WORKDIR /app
COPY --from=build /app/dist ./
EXPOSE 4000
CMD ["node", "angular-ssr/server/server.mjs"]
