FROM debian:stretch-slim

RUN set -ex \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get update \
    && apt-get -y --no-install-recommends install \
        dumb-init rsyslog lnav \
        net-tools iproute2 ifupdown isc-dhcp-client inetutils-ping \
        telnet traceroute procps nano vim-tiny \
    && rm -rf /var/lib/apt/lists/* \
#
# rsyslog configuration
#
    && sed -i.orig -E -e '/imudp|imtcp/ s/^#//' -e '/^[^#].*klog/ s/^/#/' /etc/rsyslog.conf \
    && /bin/echo -e '\
\x23\n\
\x23 Logging from network devices\n\
\x23\n\
if not ($fromhost-ip startswith "127.") then {\n\
	-/var/log/network\n\
	stop\n\
}' \
        > /etc/rsyslog.d/40-network.conf \
#
# logview script
#
    && /bin/echo -e '\
\x23!/bin/sh\n\
\n\
getopt -q -s sh "hHI:iuCd:Vartw:c:f:nq" "$@" | grep -q -- " --$"\n\
if [ $? -eq 0 ]; then\n\
	lnav "$@" /var/log/syslog /var/log/network\n\
else\n\
	lnav "$@"\n\
fi' \
        > /usr/local/bin/logview && chmod +x /usr/local/bin/logview \
#
# startup script
#
    && /bin/echo -e '\
\x23!/bin/sh\n\
[ $$ -eq 1 ] && exec dumb-init -- "$0" "$@"\n\
\n\
service rsyslog start\n\
[ -e /var/log/network ] || touch /var/log/network\n\
\n\
cd\n\
logview\n\
echo\n\
echo "Use \"logview\" to view the logs."\n\
echo\n\
exec bash -i' \
        > /etc/init.sh && chmod +x /etc/init.sh

VOLUME [ "/root", "/var/log" ]
CMD [ "/etc/init.sh" ]
