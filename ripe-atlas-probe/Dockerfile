# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM
FROM $BUILD_FROM

# Execute during the build of the image
ARG TEMPIO_VERSION BUILD_ARCH
RUN \
    curl -sSLf -o /usr/bin/tempio \
    "https://github.com/home-assistant/tempio/releases/download/${TEMPIO_VERSION}/tempio_${BUILD_ARCH}"

# Copy root filesystem
COPY rootfs /

# Rest of the Dockerfile inspired by https://github.com/CTassisF/ripe-atlas-alpine
RUN apk add --no-cache autoconf automake g++ gcc git libtool linux-headers make openssl-dev patch sed &&\
 git clone --recursive https://github.com/RIPE-NCC/ripe-atlas-software-probe.git &&\
 sed -i '/fakeroot.*dpkg-deb/Q' ripe-atlas-software-probe/build-config/debian/bin/make-deb &&\
 ./ripe-atlas-software-probe/build-config/debian/bin/make-deb &&\
 mkdir /var/atlasdata

COPY /atlasswprobe-*-1/usr/local/atlas/ /usr/local/atlas/
COPY /ripe-atlas-software-probe/bin/ /usr/local/atlas/bin/
COPY /atlasswprobe-*-1/var/atlas-probe/ /var/atlas-probe/
RUN apk add --no-cache libcap net-tools openssh tini &&\
 adduser -D atlas &&\
 ln -s /usr/local/atlas/bin/ATLAS /usr/local/bin/atlas &&\
 echo "CHECK_ATLASDATA_TMPFS=no" > /var/atlas-probe/state/config.txt &&\
 echo "RXTXRPT=yes" >> /var/atlas-probe/state/config.txt &&\
 setcap cap_net_raw=ep /usr/local/atlas/bb-13.3/bin/busybox
WORKDIR /var/atlas-probe
VOLUME ["/var/atlas-probe/etc", "/var/atlas-probe/status"]
ENTRYPOINT ["/sbin/tini", "--"]
# Image expects a tmpfs to be mounted at /var/atlasdata
CMD atlas