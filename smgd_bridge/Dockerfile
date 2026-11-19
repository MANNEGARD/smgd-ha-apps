# SMGD Bridge – byggs av Home Assistant själv (config.yaml har ingen image:-rad), så ingen container-registry
# behövs. bridge.mjs är bryggans färdiga paket (esbuild) från MANNEGARD/smgd-developer-lab (apps/bridge), samma
# version som i config.yaml. Körberoendena hämtas från npm vid bygget: LG:s webOS-CLI, ssh2 och – för den valfria
# VCI-drivrutinen – serialport och dbus-next. Inga installationsskript körs. Appen öppnar ingen port.
FROM node:22-alpine AS deps
ARG WEBOS_CLI_VERSION=3.2.6
ARG SSH2_VERSION=1.17.0
ARG SERIALPORT_VERSION=13.0.0
ARG DBUS_NEXT_VERSION=0.10.2
RUN mkdir -p /app /opt/webos-cli \
 && npm install --prefix /opt/webos-cli --omit=dev --no-audit --no-fund --ignore-scripts "@webos-tools/cli@${WEBOS_CLI_VERSION}" \
 && npm install --prefix /app --omit=dev --omit=optional --no-audit --no-fund --ignore-scripts "ssh2@${SSH2_VERSION}" "serialport@${SERIALPORT_VERSION}" "dbus-next@${DBUS_NEXT_VERSION}"

# Alltid Node 22 på Alpine – inte BUILD_FROM: Home Assistants egen basavbild (med s6-overlay som init) gav
# "s6-overlay-suexec: fatal: can only run as pid 1". Ingen ärvd ENTRYPOINT; bryggan startas direkt.
FROM node:22-alpine

ARG BUILD_VERSION=0.0.0

ENV NODE_ENV=production \
    SMGD_BRIDGE_DATA_DIR=/data \
    SMGD_ARES_BIN_DIR=/opt/webos-cli/node_modules/.bin \
    DBUS_SYSTEM_BUS_ADDRESS=unix:path=/run/dbus/system_bus_socket

COPY --from=deps /opt/webos-cli /opt/webos-cli
COPY --from=deps /app /app
COPY bridge.mjs /app/bridge.mjs
WORKDIR /app

# Bryggan lyssnar inte på något; hälsan är att processen håller sin status färsk.
HEALTHCHECK --interval=60s --timeout=10s --start-period=45s --retries=3 CMD ["node", "/app/bridge.mjs", "--healthcheck"]

LABEL io.hass.type="addon" \
      io.hass.version="${BUILD_VERSION}" \
      io.hass.name="SMGD Bridge" \
      io.hass.description="Local agent for SMGD Developer Lab" \
      org.opencontainers.image.title="SMGD Bridge" \
      org.opencontainers.image.source="https://github.com/MANNEGARD/smgd-ha-apps"

ENTRYPOINT []
CMD ["node", "/app/bridge.mjs"]
