FROM alpine

#
# install iouyap and (frozen) ioutools
#
RUN set -ex \
    && dd if=/dev/zero bs=4 count=1 of=/etc/hostid \
    && echo -e '\
@edge http://dl-cdn.alpinelinux.org/alpine/edge/main\n\
@edge http://dl-cdn.alpinelinux.org/alpine/edge/community' \
        >> /etc/apk/repositories \
    && apk add --update-cache py2-cx_freeze@edge iouyap@edge wget \
    && cd /tmp \
    && wget -q --no-check-certificate https://raw.githubusercontent.com/ehlers/IOUtools/master/iou_import \
    && wget -q --no-check-certificate https://raw.githubusercontent.com/ehlers/IOUtools/master/iou_export \
    && ln -s `which ldd` ldd \
    && cxfreeze iou_import --target-dir /usr/local/bin \
    && cxfreeze iou_export --target-dir /usr/local/bin \
    && rm /usr/local/bin/ldd \
    && rm iou_export iou_import ldd \
    && cd - \
    && apk del py2-cx_freeze wget \
    && rm -rf /var/cache/apk/*

ADD libc-i386.tar.gz /
ADD https://github.com/ehlers/start-stop/releases/download/v0.1/start-stop_0.1_amd64 /usr/local/bin/start-stop
ADD do-start do-stop /usr/local/bin/

RUN cd /usr/local/bin; chmod 755 start-stop do-start do-stop

CMD [ "start-stop" ]