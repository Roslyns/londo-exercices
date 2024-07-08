# syntax=docker/dockerfile:1.4

FROM node:lts-alpine AS build

WORKDIR /app
COPY package*.json ./

RUN npm ci
COPY ./ /app

ENV CI=true
ENV NODE_ENV=production
ENV PORT=3000
RUN npm cache clean --force
CMD ["npm", "run", "start:prod"]