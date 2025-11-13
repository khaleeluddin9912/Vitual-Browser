# Use a more recent Debian base
FROM debian:bookworm-slim

# Install ca-certificates FIRST before any curl operations
RUN apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates && \
    update-ca-certificates && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install remaining packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    openbox \
    curl \
    xz-utils \
    libgtk-3-0 \
    libdbus-glib-1-2 \
    pulseaudio \
    xorg \
    xserver-xorg-video-dummy \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Create neko user
RUN useradd -m -d /home/neko -s /bin/bash neko

# Install Firefox with certificate fix
RUN set -eux; \
    ACTUAL_URL=$(curl -s -L -I -o /dev/null -w '%{url_effective}' "https://download.mozilla.org/?product=firefox-latest&os=linux64&lang=en-US"); \
    curl -L "$ACTUAL_URL" -o /tmp/firefox.tar.xz; \
    tar -xJf /tmp/firefox.tar.xz -C /opt; \
    rm -f /tmp/firefox.tar.xz; \
    ln -s /opt/firefox/firefox /usr/bin/firefox; \
    mkdir -p /home/neko/.mozilla/firefox/profile.default/extensions; \
    chown -R neko:neko /home/neko/.mozilla/firefox/profile.default

# Copy your configuration files
COPY supervisord.conf /etc/neko/supervisord/firefox.conf
COPY neko.js /usr/lib/firefox/mozilla.cfg
COPY autoconfig.js /usr/lib/firefox/defaults/pref/autoconfig.js
COPY policies.json /usr/lib/firefox/distribution/policies.json
COPY --chown=neko profiles.ini /home/neko/.mozilla/firefox/profiles.ini
COPY openbox.xml /etc/neko/openbox.xml

# Set up entrypoint
USER neko
WORKDIR /home/neko
CMD ["firefox"]
