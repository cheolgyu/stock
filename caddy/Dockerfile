FROM caddy:2.4.3-alpine

RUN apk --no-cache add tzdata && \
    cp /usr/share/zoneinfo/Asia/Seoul /etc/localtime && \
    echo "Asia/Seoul" > /etc/timezone

COPY ./config/Caddyfile /etc/caddy/Caddyfile
COPY ./site/dist /srv