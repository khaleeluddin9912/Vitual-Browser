ARG BASE_IMAGE=m1k1o/neko:base
FROM $BASE_IMAGE

# Comprehensive security updates
RUN apt-get update && \
    apt-get dist-upgrade -y --no-install-recommends && \
    apt-get install -y --no-install-recommends apt-transport-https ca-certificates && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

#
# install firefox
RUN set -eux; apt-get update; \
    apt-get install -y --no-install-recommends openbox \
    curl xz-utils bzip2 libgtk-3-0 libdbus-glib-1-2; \
    \
    # Get the ACTUAL download URL from the redirect
    ACTUAL_URL=$(curl -s -L -I -o /dev/null -w '%{url_effective}' "https://download.mozilla.org/?product=firefox-latest&os=linux64&lang=en-US"); \
    curl -L "$ACTUAL_URL" -o /tmp/firefox.tar.xz; \
    \
    # Extract Firefox (using xz compression)
    tar -xJf /tmp/firefox.tar.xz -C /opt; \
    rm -f /tmp/firefox.tar.xz; \
    ln -s /opt/firefox/firefox /usr/bin/firefox; \
    \
    # create profile directory
    mkdir -p /home/neko/.mozilla/firefox/profile.default/extensions; \
    chown -R neko:neko /home/neko/.mozilla/firefox/profile.default; \
    \
    # clean up
    apt-get --purge autoremove -y xz-utils bzip2; \
    apt-get clean -y; \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

# Remove unnecessary packages that might have vulnerabilities
RUN apt-get remove -y --purge \
    python2.7 \
    python2.7-minimal \
    libpython2.7-stdlib \
    libpython2.7-minimal \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/cache/apt/*

#
# copy configuration files
COPY supervisord.conf /etc/neko/supervisord/firefox.conf
COPY neko.js /usr/lib/firefox/mozilla.cfg
COPY autoconfig.js /usr/lib/firefox/defaults/pref/autoconfig.js
COPY policies.json /usr/lib/firefox/distribution/policies.json
COPY --chown=neko profiles.ini /home/neko/.mozilla/firefox/profiles.ini
COPY openbox.xml /etc/neko/openbox.xml
