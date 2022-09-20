# syntax = docker/dockerfile:experimental


##
## builder
##
FROM debian:buster-slim AS builder
LABEL maintainer Yuki Takei <yuki@weseek.co.jp>

ENV NGINX_VERSION 1.19.1
ENV NGINX_PKG_RELEASE 1~buster
ENV NCHAN_VERSION 1.3.4

ENV MODULES_DIR /usr/local/nginx/modules
ENV DEBIAN_FRONTEND noninteractive

WORKDIR /usr/local/src

RUN apt-get update
RUN apt-get install --no-install-recommends --no-install-suggests -y gnupg1 ca-certificates wget
RUN apt-get install -y dpkg-dev libpcre3-dev zlib1g-dev

# retrieve nginx source
RUN echo "deb-src https://nginx.org/packages/mainline/debian/ buster nginx" >> /etc/apt/sources.list.d/nginx.list
RUN NGINX_GPGKEY=573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62; \
  KEY_SERVER=hkp://keyserver.ubuntu.com:80; \
  apt-key adv --keyserver "$KEY_SERVER" --keyserver-options timeout=10 --recv-keys "$NGINX_GPGKEY"
RUN apt-get update
RUN apt-get source nginx=${NGINX_VERSION}-${NGINX_PKG_RELEASE}

# retrieve nchan source
RUN wget "https://github.com/slact/nchan/archive/v${NCHAN_VERSION}.tar.gz" -O nchan.tar.gz
RUN tar zxf nchan.tar.gz

# build nchan module
WORKDIR /usr/local/src/nginx-${NGINX_VERSION}
RUN ./configure --with-compat --add-dynamic-module=/usr/local/src/nchan-${NCHAN_VERSION}
RUN make -f objs/Makefile objs/ngx_nchan_module.so

RUN mkdir -p ${MODULES_DIR}
RUN mv objs/ngx_nchan_module.so ${MODULES_DIR}



##
## release
##
FROM nginx:1.19.1
LABEL maintainer Yuki Takei <yuki@weseek.co.jp>

ENV MODULES_DIR /usr/local/nginx/modules

RUN mkdir -p ${MODULES_DIR}

COPY --from=builder \
  ${MODULES_DIR}/ngx_nchan_module.so ${MODULES_DIR}

RUN echo "# load Nchan module" > /tmp/nginx.conf
RUN echo "load_module ${MODULES_DIR}/ngx_nchan_module.so;" >> /tmp/nginx.conf
RUN cat /etc/nginx/nginx.conf >> /tmp/nginx.conf

RUN mv /etc/nginx/nginx.conf /etc/nginx/nginx.conf.org
RUN mv /tmp/nginx.conf /etc/nginx/nginx.conf

COPY ./conf.d/default.conf /etc/nginx/conf.d/
