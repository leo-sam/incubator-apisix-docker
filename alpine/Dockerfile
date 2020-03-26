 FROM openresty/openresty:alpine-fat

# 修改为master分支
ARG APISIX_VERSION=master
LABEL apisix_version="${APISIX_VERSION}"
#添加国内镜像
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

ADD ./rockspec /app/rockspec

RUN apk add --no-cache --virtual .builddeps \
    automake \
    autoconf \
    libtool \
    pkgconfig \
    cmake \
    git \
    && luarocks install /app/rockspec/apisix-${APISIX_VERSION}-0.rockspec --tree=/usr/local/apisix/deps \
    && cp /usr/local/apisix/deps/lib/luarocks/rocks-5.1/apisix/${APISIX_VERSION}-0/bin/apisix /usr/bin/ \
    && bin='#! /usr/local/openresty/luajit/bin/luajit\npackage.path = "/usr/local/apisix/lua/?.lua;" .. package.path' \
    && sed -i "1s@.*@$bin@" /usr/bin/apisix \
    && mv /usr/local/apisix/deps/share/lua/5.1/apisix/lua /usr/local/apisix \
    && apk del .builddeps

WORKDIR /usr/local/apisix

EXPOSE 9080 9443

CMD ["sh", "-c", "/usr/bin/apisix init && /usr/bin/apisix init_etcd && /usr/local/openresty/bin/openresty -p /usr/local/apisix -g 'daemon off;'"]
