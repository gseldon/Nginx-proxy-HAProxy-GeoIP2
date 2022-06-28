FROM nginx:stable-alpine
ARG NODE

ENV NODE=$NODE
ENV SSL_FOLDER=/etc/nginx/ssl
ENV MAXMID_URL=https://dl.miyuru.lk/geoip/maxmind

RUN apk add --no-cache openssl libmaxminddb nginx-mod-http-geoip

RUN mkdir -p /etc/nginx/ssl/ \
    # generate a certificate for the default site
    && openssl req \
    -new -x509 \
    -nodes -out ${SSL_FOLDER}/default-cert.pem \
    -keyout ${SSL_FOLDER}/default-key.pem \
    -subj '/C=RU/ST=MSK/L=Moskow/O=My Inc/OU=DevOps/CN=proxy/emailAddress=it@npcmr.ru' \
    &&  curl https://ssl-config.mozilla.org/ffdhe2048.txt > ${SSL_FOLDER}/ssl-dhparams.pem

HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 CMD [ "curl", "-f", "http://localhost/basic_status" ]

RUN cd /var/lib; \
    mkdir -p nginx; \
    wget -q -O- ${MAXMID_URL}/country/maxmind.dat.gz | gunzip -c > nginx/maxmind-country.dat; \
    wget -q -O- ${MAXMID_URL}/city/maxmind.dat.gz | gunzip -c > nginx/maxmind-city.dat; \
    chown -R nginx. nginx

RUN sed -i -- "s/nginx/Proxy Server $NODE/" /usr/share/nginx/html/index.html

CMD [ "/bin/sh -c 'while :; do sleep 1m & wait $${!}; nginx -s reload; done & nginx -g \"daemon off;\"'" ]