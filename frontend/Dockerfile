# syntax=docker/dockerfile:1.4

FROM node:lts AS builder

ENV CI=true
WORKDIR /code

COPY package*.json ./
RUN npm ci

COPY . .

RUN npm run build

FROM nginx:alpine

COPY --from=builder /code/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]