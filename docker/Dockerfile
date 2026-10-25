# Packaging used by this fork's CI (docker.yml). Mirrors the deployment
# service layout: debian slim + JRE + tini, unprivileged uid 1000, data dir
# /home/signaluser/.local/share/signal-cli. Built from the signed upstream
# release tarball pinned in docker/version.lock.
FROM debian:trixie-slim
RUN apt-get update && apt-get install -y --no-install-recommends \
  ca-certificates tini openjdk-25-jre-headless curl qrencode && rm -rf /var/lib/apt/lists/*

ARG VERSION
ARG SHA256
RUN curl -fsSL -o /tmp/signal.tar.gz \
    "https://github.com/AsamK/signal-cli/releases/download/v${VERSION}/signal-cli-${VERSION}.tar.gz" \
  && echo "${SHA256}  /tmp/signal.tar.gz" | sha256sum -c \
  && tar xf /tmp/signal.tar.gz -C /opt \
  && ln -sf /opt/signal-cli-${VERSION}/bin/signal-cli /usr/local/bin/signal-cli \
  && rm /tmp/signal.tar.gz

RUN useradd -m -u 1000 signaluser
USER signaluser
WORKDIR /home/signaluser/.local/share/signal-cli
EXPOSE 8080
ENTRYPOINT ["/usr/bin/tini", "--", "/usr/local/bin/signal-cli"]
