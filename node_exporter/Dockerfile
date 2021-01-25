FROM python:slim

# runtime dependencies
RUN set -ex; \
    ln -s /usr/local/bin/python3 /usr/bin/python3; \
    /usr/local/bin/python -m pip install --upgrade pip; \
    pip3 install toml; \
    apt-get update; \
    apt-get install -y --no-install-recommends curl cron; \
    apt-get upgrade -y; \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
    rm -rf /var/lib/apt/lists/*

RUN set -ex; \
    \
    curl -fsSL -o /usr/local/bin/lotus-exporter-farcaster.py https://raw.fastgit.org/s0nik42/lotus-farcaster/main/lotus-exporter-farcaster/lotus-exporter-farcaster.py; \
    sed -i 's#str(Path.home())#"/path"#g' /usr/local/bin/lotus-exporter-farcaster.py; \
    chmod +x /usr/local/bin/lotus-exporter-farcaster.py; \
    curl -fsSL -o /tmp/lotus-exporter-farcaster.cron https://raw.fastgit.org/s0nik42/lotus-farcaster/main/lotus-exporter-farcaster/lotus-exporter-farcaster.cron; \
    cat /tmp/lotus-exporter-farcaster.cron |sed "s/LOTUS_USER//" > /etc/cron.d/lotus-exporter-farcaster; \
    chmod 0644 /etc/cron.d/lotus-exporter-farcaster; \
    mkdir -p /var/lib/prometheus/node-exporter; \
    crontab /etc/cron.d/lotus-exporter-farcaster

ENV NODE_EXPORTER_VERSION 1.0.1
RUN set -ex; \
    \
    curl -fsSL -o /tmp/node_exporter-${NODE_EXPORTER_VERSION}.linux-amd64.tar.gz https://github.com/prometheus/node_exporter/releases/download/v${NODE_EXPORTER_VERSION}/node_exporter-${NODE_EXPORTER_VERSION}.linux-amd64.tar.gz; \
    cd /tmp && tar xvzf node_exporter-${NODE_EXPORTER_VERSION}.linux-amd64.tar.gz; \
    mv /tmp/node_exporter-${NODE_EXPORTER_VERSION}.linux-amd64/node_exporter /usr/local/bin

CMD cron && node_exporter --collector.textfile.directory=/var/lib/prometheus/node-exporter
