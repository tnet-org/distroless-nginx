# Copyright (c) 2025 TNET
# SPDX-License-Identifier: BSD-2-Clause OR MIT OR Apache-2.0
# Build NGINX

FROM debian:bookworm-slim AS builder
LABEL org.opencontainers.image.source="https://github.com/tnetmoe/distroless-nginx"
LABEL org.opencontainers.image.description="A secure and lightweight Nginx image."
LABEL org.opencontainers.image.licenses="BSD-2-Clause OR MIT OR Apache-2.0"

ARG NGINX_VERSION
ARG MODSECURITY_VERSION
ARG MODSECURITY_NGINX_VERSION
ARG GEOIP2_VERSION

# Install dependencies
RUN DEBIAN_FRONTEND=noninteractive apt-get update -qq \
	&& apt-get upgrade -qq -y \
	&& apt-get install -qq -y --no-install-recommends \
	build-essential \
	curl \
	git \
	ca-certificates \
	autoconf \
	automake \
	libtool \
	pkg-config \
	libpcre3-dev \
	libpcre2-dev \
	zlib1g-dev \
	libssl-dev \
	libcurl4-openssl-dev \
	libyajl-dev \
	libxml2-dev \
	libfuzzy-dev \
	liblua5.3-dev \
	cmake \
	# GeoIP library
    libgeoip-dev \
	# GeoIP2 library
	libmaxminddb-dev

WORKDIR /build
RUN git clone --depth=1 --branch ${MODSECURITY_VERSION} https://github.com/owasp-modsecurity/ModSecurity.git modsecurity

#WORKDIR /usr/local/opt/modsecurity 
WORKDIR /build/modsecurity
RUN git submodule init \
	&& git submodule update
RUN ./build.sh \
	&& ./configure --with-pcre2 \
	&& make -j$(nproc) \
	&& make install

WORKDIR /build

# Download ModSecurity-nginx connector
RUN git clone --depth 1 --branch ${MODSECURITY_NGINX_VERSION} https://github.com/owasp-modsecurity/ModSecurity-nginx.git modsecurity-nginx

# Clone GeoIP2 module
RUN git clone --depth=1 --branch ${GEOIP2_VERSION} https://github.com/leev/ngx_http_geoip2_module.git ngx_http_geoip2_module

# Download NGINX source
RUN curl -O http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz

# Extract NGINX source
RUN mkdir nginx \
	&& tar zxvf nginx-${NGINX_VERSION}.tar.gz -C nginx --strip-components=1

WORKDIR /build/nginx

# Build NGINX
RUN ./configure \
	--prefix=/usr/local/nginx \
	--conf-path=/etc/nginx/nginx.conf \
	--error-log-path=/var/log/nginx/error.log \
	--http-log-path=/var/log/nginx/access.log \
	--user=nonroot \
	--group=nonroot \
	--with-http_ssl_module \
	--with-http_v2_module \
	--with-http_realip_module \
	--with-http_slice_module \
	--with-http_stub_status_module \
	--with-http_geoip_module \
	--with-http_gzip_static_module \
	--with-poll_module \
	--with-select_module \
	--with-threads \
	--without-http_userid_module \
	--without-http_auth_basic_module \
	--without-http_empty_gif_module \
	--without-http_browser_module \
	--without-http_grpc_module \
	--without-http_mirror_module \
	--add-dynamic-module=/build/modsecurity-nginx \
	--add-dynamic-module=/build/ngx_http_geoip2_module \
	&& make -j$(nproc) \
	&& make install

# Prepare libraries required for runtime
WORKDIR /build/lib
RUN set -eux; \
	# Use ldd to find runtime deps
	ldd /usr/local/nginx/sbin/nginx \
		/usr/local/modsecurity/lib/libmodsecurity.so.3 \
		/usr/local/nginx/modules/*.so \
	| awk '/=>/ { print $(NF-1) }' \
	| sort -u \
	| xargs -I '{}' cp -v '{}' /build/lib/ \
	&& cp -v /usr/local/modsecurity/lib/libmodsecurity.so.3 /build/lib/

# Final
FROM gcr.io/distroless/cc-debian12:nonroot

# Copy runtime deps
COPY --from=builder /build/lib /usr/lib/

# Copy files from build stages
# 1002:1000 is the noroot user
COPY --from=builder --chown=1002:1000 /usr/local/nginx /usr/local/nginx

# Copy default NGINX assets
COPY --from=builder --chown=1002:1000 /build/nginx/html /usr/local/nginx/html
WORKDIR /etc/nginx
COPY --from=builder --chown=1002:1000 /build/nginx/conf/mime.types mime.types
COPY --from=builder --chown=1002:1000 /build/modsecurity/unicode.mapping unicode.mapping
COPY --from=builder --chown=1002:1000 /build/modsecurity/modsecurity.conf-recommended modsecurity.conf-recommended 

# Copy configuration files
COPY --chown=1002:1000 ./images/modsecurity/nginx.conf nginx.conf
WORKDIR /etc/nginx/conf.d
COPY --chown=1002:1000 ./images/modsecurity/default.conf default.conf

# For my sanity: switch to distroless noroot user to be extra sure
USER nonroot

# Command to start NGINX
CMD ["/usr/local/nginx/sbin/nginx", "-g", "daemon off;"]
