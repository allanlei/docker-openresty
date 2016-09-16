FROM        alpine:3.4

ARG         RESTY_VERSION="1.11.2.1"
ARG         RESTY_CONFIG_OPTIONS="\
                --with-file-aio \
                --with-http_addition_module \
                --with-http_auth_request_module \
                --with-http_dav_module \
                --with-http_flv_module \
                --with-http_geoip_module \
                --with-http_gunzip_module \
                --with-http_gzip_static_module \
                --with-http_image_filter_module \
                --with-http_mp4_module \
                --with-http_random_index_module \
                --with-http_realip_module \
                --with-http_secure_link_module \
                --with-http_slice_module \
                --with-http_ssl_module \
                --with-http_stub_status_module \
                --with-http_sub_module \
                --with-http_v2_module \
                --with-http_xslt_module \
                --with-ipv6 \
                --with-pcre-jit \
                --with-stream \
                --with-stream_ssl_module \
                --with-threads \
            "

RUN         apk add --no-cache --virtual .build-deps \
                build-base \
                curl \
                gd-dev \
                geoip-dev \
                libxslt-dev \
                linux-headers \
                make \
                perl-dev \
                readline-dev \
                zlib-dev \
                openssl-dev \
                pcre-dev && \
            cd /tmp && \
            curl -fSL https://openresty.org/download/openresty-${RESTY_VERSION}.tar.gz | tar xz && \
            cd openresty-${RESTY_VERSION} && \
            ./configure ${RESTY_CONFIG_OPTIONS} && \
            make -j$(getconf _NPROCESSORS_ONLN) && \
            make install && \
            rm -rf /tmp/* && \
            runDeps="$( \
                scanelf --needed --nobanner /usr/local/openresty/bin/openresty \
                    | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
                    | sort -u \
                    | xargs -r apk info --installed \
                    | sort -u \
        	)" && \
	        apk --no-cache add --virtual .run-deps \
                $runDeps \
                libgcc && \
            apk del .build-deps && \
            ln -sf /usr/local/openresty/bin/openresty /usr/local/bin/openresty && \
            mkdir -p /usr/local/openresty/nginx/conf/conf.d && \
            ln -sf /dev/stdout /usr/local/openresty/nginx/logs/access.log && \
            ln -sf /dev/stderr /usr/local/openresty/nginx/logs/error.log

CMD         ["openresty", "-g", "daemon off;"]
