FROM node:latest

ARG BUILD_ENV=${BUILD_ENV}

WORKDIR /app

COPY package.json ./

RUN npm install

EXPOSE 3000

CMD [ "npm", "start" ]