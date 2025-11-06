ARG BASE_IMAGE=m1k1o/neko:base
FROM $BASE_IMAGE

# Fixed Firefox URL - direct download
ARG SRC_URL="https://download-installer.cdn.mozilla.net/pub/firefox/releases/latest/linux-x86_64/en-US/firefox-latest.tar.bz2"

#
# install firefox
RUN set -eux; apt-get update; \
    apt-get install -y --no-install-recommends openbox \
    xz-utils bzip2 libgtk-3-0 libdbus-glib-1-2; \
    \
    # Fixed: Use direct download URL
    wget -O /tmp/firefox.tar.bz2 "${SRC_URL}"; \
    tar -xjf /tmp/firefox.tar.bz2 -C /usr/lib; \
    rm -f /tmp/firefox.tar.bz2; \
    ln -s /usr/lib/firefox/firefox /usr/bin/firefox; \
    \
    # create profile directory
    mkdir -p /home/neko/.mozilla/firefox/profile.default/extensions; \
    chown -R neko:neko /home/neko/.mozilla/firefox/profile.default; \
    \
    # clean up
    apt-get --purge autoremove -y xz-utils bzip2; \
    apt-get clean -y; \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

#
# copy configuration files
COPY supervisord.conf /etc/neko/supervisord/firefox.conf
COPY neko.js /usr/lib/firefox/mozilla.cfg
COPY autoconfig.js /usr/lib/firefox/defaults/pref/autoconfig.js
COPY policies.json /usr/lib/firefox/distribution/policies.json
COPY --chown=neko profiles.ini /home/neko/.mozilla/firefox/profiles.ini
COPY openbox.xml /etc/neko/openbox.xml
