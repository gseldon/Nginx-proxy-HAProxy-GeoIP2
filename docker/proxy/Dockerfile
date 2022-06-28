ARG NGINX_VERSION=1.21
FROM nginx:${NGINX_VERSION} as build

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install --no-install-recommends -y \
    build-essential \
    git \
    openssl \
    libpcre3-dev \
    libssl-dev \
    zlib1g-dev \
    libmaxminddb0 libmaxminddb-dev mmdb-bin

RUN set -x \
    && git clone https://github.com/leev/ngx_http_geoip2_module \
    && echo "deb-src http://nginx.org/packages/mainline/debian/ bullseye nginx" >> /etc/apt/sources.list \
    && DEBIAN_FRONTEND=noninteractive apt-get update \
    && apt-get source nginx=${NGINX_VERSION} \
    && cd nginx-${NGINX_VERSION} \
    && ./configure \
        --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-g -O2 -fdebug-prefix-map=/data/builder/debuild/nginx-1.13.5/debian/debuild-base/nginx-1.13.5=. -specs=/usr/share/dpkg/no-pie-compile.specs -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' --with-ld-opt='-specs=/usr/share/dpkg/no-pie-link.specs -Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie' \
        --add-dynamic-module=/ngx_http_geoip2_module \
    && make modules
    # && cp objs/ngx_http_geoip2_module.so /usr/lib/nginx/modules/ \

FROM nginx:${NGINX_VERSION}

ARG NODE
ENV NODE=$NODE
ENV RELOAD_TIME=1m
ENV SSL_FOLDER=/etc/nginx/ssl

COPY --from=build /nginx-${NGINX_VERSION}/objs/ngx_http_geoip2_module.so /usr/lib/nginx/modules/

RUN apt-get update \
    && apt-get install --no-install-recommends --no-install-suggests -y \
    libmaxminddb0 \
    libmaxminddb-dev \
    mmdb-bin \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /etc/nginx/ssl/ \
    # generate a certificate for the default site
    && openssl req \
    -new -x509 \
    -nodes -out ${SSL_FOLDER}/default-cert.pem \
    -keyout ${SSL_FOLDER}/default-key.pem \
    -subj '/C=RU/ST=MSK/L=Moskow/O=My Inc/OU=DevOps/CN=proxy/emailAddress=it@npcmr.ru' \
    &&  curl https://ssl-config.mozilla.org/ffdhe2048.txt > ${SSL_FOLDER}/ssl-dhparams.pem

# https://github.com/P3TERX/GeoLite.mmdb
RUN curl -Li https://git.io/GeoLite2-City.mmdb --output /usr/share/nginx/GeoLite2-City.mmdb && \
    curl -Li https://git.io/GeoLite2-Country.mmdb --output /usr/share/nginx/GeoLite2-Country.mmdb

HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 CMD [ "curl", "-f", "http://localhost/basic_status" ]

RUN sed -i -- "s/nginx/Proxy Server $NODE/" /usr/share/nginx/html/index.html

CMD [ "/bin/sh -c 'while :; do sleep $RELOAD_TIME & wait $${!}; nginx -s reload; done & nginx -g \"daemon off;\"'" ]