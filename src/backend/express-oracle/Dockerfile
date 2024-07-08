# syntax=docker/dockerfile:1.4

# 1. setup oracle
FROM oraclelinux:7-slim as builder

ARG release=19
ARG update=5

RUN yum -y install oracle-release-el7 && \
    yum -y install oracle-instantclient${release}.${update}-basiclite && \
    rm -rf /usr/lib/oracle/${release}.${update}/client64/bin

WORKDIR /usr/lib/oracle/${release}.${update}/client64/lib/
RUN rm -rf *jdbc* *occi* *mysql* *jar



# 1. setup nodejs app
FROM node:lts-alpine

COPY --from=builder /usr/lib/oracle /usr/lib/oracle
COPY --from=builder /usr/share/oracle /usr/share/oracle
COPY --from=builder /etc/ld.so.conf.d/oracle-instantclient.conf /etc/ld.so.conf.d/oracle-instantclient.conf

RUN apk update && \
    apk upgrade && \
    apk add --no-cache libaio

ENV ORACLE_BASE=/usr/lib/oracle
ENV LD_LIBRARY_PATH=/usr/lib/oracle/${release}.${update}/client64/lib
ENV TNS_ADMIN=/usr/lib/oracle/${release}.${update}/client64/network/admin
ENV ORACLE_HOME=/usr/lib/oracle/${release}.${update}/client64
ENV CI=true
ENV NODE_ENV=production

WORKDIR /app
COPY package*.json .

RUN npm ci
COPY . .

RUN npm cache clean --force
ENV PORT=3000

CMD ["npm", "run", "start:prod"]