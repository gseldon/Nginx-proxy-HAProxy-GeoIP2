FROM nginx:latest
ARG NODE

ENV NODE=$NODE

RUN sed -i -- "s/nginx/Proxy Server $NODE/" /usr/share/nginx/html/index.html