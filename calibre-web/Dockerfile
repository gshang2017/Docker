FROM alpine:3.13

ARG  S6_VER=2.2.0.3
ARG  CALIBRE_WEB_VER=0.6.12
ARG  CALIBRE_VER=5.10.1
ARG  KEPUBIFY_VER=3.1.6

ENV USER=
ENV PASSWORD=
ENV WEBLANGUAGE=zh_CN
ENV LANG=C.UTF-8
ENV TZ=Asia/Shanghai
ENV QTWEBENGINE_CHROMIUM_FLAGS=--no-sandbox
ENV CALIBREDB_OTHER_OPTION=

COPY  root  /
RUN apk add --no-cache bash ca-certificates  tzdata python3 py3-pip unrar expect imagemagick6 	libintl libstdc++ libxcb mesa-gl mesa-gles bash-completion libxi libxcomposite freetype fontconfig libidn nss eudev  libxrender libxcursor libxtst libxrandr libxkbcommon libatomic ttf-dejavu inotify-tools \
&&  apk add --no-cache --virtual  calibrewebdep build-base libffi-dev git python3-dev openldap-dev libxml2-dev libxslt-dev libjpeg-turbo-dev libevent-dev file cython  \
#install s6-overlay
&&  if [ "$(uname -m)" = "x86_64" ];then s6_arch=amd64;elif [ "$(uname -m)" = "aarch64" ];then s6_arch=aarch64;elif [ "$(uname -m)" = "armv7l" ];then s6_arch=arm; fi  \
&&  wget --no-check-certificate https://github.com/just-containers/s6-overlay/releases/download/v${S6_VER}/s6-overlay-${s6_arch}.tar.gz  \
&&  tar -xvzf s6-overlay-${s6_arch}.tar.gz  \
#install  calibre-ebook
&&  if [ "$(uname -m)" = "x86_64" ];then calibre_arch=x86_64;elif [ "$(uname -m)" = "aarch64" ];then calibre_arch=aarch64;elif [ "$(uname -m)" = "armv7l" ];then calibre_arch=armv7; fi  \
&&  wget -P /tmp  https://github.com/gshang2017/bypy/releases/download/v${CALIBRE_VER}/calibre-${CALIBRE_VER}-${calibre_arch}.musl.txz \
&&  mkdir -p /opt/calibre && rm -rf /opt/calibre/* && tar xvf /tmp/calibre-${CALIBRE_VER}-${calibre_arch}.musl.txz -C /opt/calibre && /opt/calibre/calibre_postinstall \
#install  calibre-web
&&  wget -P /tmp  https://github.com/janeczku/calibre-web/archive/${CALIBRE_WEB_VER}.zip  \
&&  unzip -d /tmp      /tmp/${CALIBRE_WEB_VER}.zip   \
&&  cp -rf  /tmp/calibre-web-${CALIBRE_WEB_VER}/*  /usr/local/calibre-web/  \
#douban api
#&& sed -i s/0df993c66c0c636e29ecbb5344252a4a/054022eaeae0b00e0fc068c0c0a2102a/g /usr/local/calibre-web/cps/static/js/get_meta.js   \
#install calibre-web dep
&&  apk add --no-cache py3-lxml py3-cryptography \
#&&  apk add --no-cache 'py3-six<1.15.0' --repository http://dl-4.alpinelinux.org/alpine/v3.11/main  \
&&  pip3  install  --no-cache-dir  -r /usr/local/calibre-web/requirements.txt  \
&&  pip3  install  --no-cache-dir  -r /usr/local/calibre-web/optional-requirements.txt  \
#install kepubify
&&  if [ "$(uname -m)" = "x86_64" ];then kepubify_arch=64bit;elif [ "$(uname -m)" = "aarch64" ];then kepubify_arch=arm64;elif [ "$(uname -m)" = "armv7l" ];then kepubify_arch=arm;  fi  \
&&  wget https://github.com/geek1011/kepubify/releases/download/v${KEPUBIFY_VER}/kepubify-linux-${kepubify_arch} -O /usr/local/bin/kepubify   \
#clean
&&  apk del  calibrewebdep  \
&&  rm s6-overlay-${s6_arch}.tar.gz   \
&&  rm -rf /var/cache/apk/*  \
&&  rm -rf /tmp/*  \
&&  chmod a+x /usr/local/calibre-server/useradd.sh   \
&&  chmod a+x /usr/local/calibre-server/languagechange.sh   \
&&  chmod a+x /usr/local/bin/kepubify

VOLUME /library  /config /autoaddbooks
EXPOSE 8080 8083
ENTRYPOINT [ "/init" ]
