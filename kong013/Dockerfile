FROM alpine:3.7

ENV KONG_VERSION 0.13.1
ENV KONG_SHA256 caf119ce7a367b9d9cd1ace55c4b6a499ff3c4693f80ea25129ebf2da0373fcc 

RUN apk add --no-cache --virtual .build-deps wget tar ca-certificates \
    && apk add --no-cache libgcc openssl pcre perl tzdata curl git \
    && wget -O kong.tar.gz "https://bintray.com/kong/kong-community-edition-alpine-tar/download_file?file_path=kong-community-edition-$KONG_VERSION.apk.tar.gz" \
    && echo "$KONG_SHA256 *kong.tar.gz" | sha256sum -c - \
    && tar -xzf kong.tar.gz -C /tmp \
    && rm -f kong.tar.gz \
    && cp -R /tmp/usr / \
    && rm -rf /tmp/usr \
    && cp -R /tmp/etc / \
    && rm -rf /tmp/etc \
    && export https_proxy=http://10.135.186.25:3128 \
    && luarocks install kong-prometheus-plugin \
    && apk del .build-deps tar wget git

# sysctl optimized by linhaitao
COPY etc/sysctl.conf /etc/sysctl.conf

# 适配 prometheus plugin(lua shared dict)
COPY nginx_kong.lua /usr/local/share/lua/5.1/kong/templates/nginx_kong.lua
# 统一的初始化参数模板
COPY kong_defaults.lua /usr/local/share/lua/5.1/kong/templates/kong_defaults.lua

COPY docker-entrypoint.sh /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 8000 8443 8001 8444

STOPSIGNAL SIGTERM

CMD ["/usr/local/openresty/nginx/sbin/nginx", "-c", "/usr/local/kong/nginx.conf", "-p", "/usr/local/kong/"]
