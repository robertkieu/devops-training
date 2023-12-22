FROM node:latest

ARG BUILD_ENV=${BUILD_ENV}

WORKDIR /usr/src/app

COPY package.json ./

RUN npm install

EXPOSE 3000
WORKDIR /app
CMD [ "npm", "start" ]