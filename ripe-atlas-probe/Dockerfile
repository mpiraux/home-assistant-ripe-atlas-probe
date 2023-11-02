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

ARG ATLAS_UID=101
ARG ATLAS_GID=999
RUN ln -s /bin/true /bin/systemctl \
	&& adduser --system --uid $ATLAS_UID atlas \
	&& addgroup -S -g $ATLAS_GID atlas \
	&& usermod -aG atlas atlas \
	&& apt-get update -y \
	&& apt-get install -y libcap2-bin iproute2 openssh-client procps net-tools gosu \
	&& rm -rf /var/lib/apt/lists/*

COPY --from=jamesits/ripe-atlas:latest /usr/local/atlas /usr/local/atlas
COPY --from=jamesits/ripe-atlas:latest /usr/local/atlas /usr/local/atlas
COPY --from=jamesits/ripe-atlas:latest /var/atlas-probe /var/atlas-probe
COPY --from=jamesits/ripe-atlas:latest /var/atlasdata /var/atlasdata
COPY --from=jamesits/ripe-atlas:latest /usr/local/bin/entrypoint.sh /usr/local/bin/entrypoint.sh

RUN ln -s /usr/local/atlas/bin/ATLAS /usr/local/bin/atlas

WORKDIR /var/atlas-probe
VOLUME [ "/var/atlas-probe/etc", "/var/atlas-probe/status" ]

ENTRYPOINT [ "entrypoint.sh" ]
CMD [ "atlas" ]