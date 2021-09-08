FROM quay.io/freeipa/freeipa-openshift-container:freeipa-server@sha256:fdb519e7a509c4309e46af6cf16b2d33fbcf2b52a7d9dfdbd404ee59533ef5e9

COPY init-data /usr/local/sbin/init

# Enable debug for gssproxy
COPY gssproxy.conf /data-template/etc/gssproxy/gssproxy.conf

VOLUME /data

ENTRYPOINT ["/usr/local/sbin/init"]
