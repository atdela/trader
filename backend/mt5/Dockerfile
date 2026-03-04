# Base image (use bookworm to avoid backports issues)
FROM ghcr.io/linuxserver/baseimage-kasmvnc:debianbookworm

# Set environment
ENV DEBIAN_FRONTEND=noninteractive
ENV WINEPREFIX=/config/.wine
ENV WINEARCH=win64
ENV DISPLAY=:1

# Remove any broken backports repos (safety)
RUN sed -i '/backports/d' /etc/apt/sources.list || true && \
    rm -f /etc/apt/sources.list.d/*backports* || true

# Install required packages
RUN dpkg --add-architecture i386 && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
    wget \
    wine \
    wine64 \
    wine32:i386 \
    winbind \
    cabextract \
    unzip \
    xvfb \
    xauth \
    python3 \
    python3-pip \
    dos2unix \
    netcat-openbsd \
    ca-certificates \
    dbus-x11 \
    && rm -rf /var/lib/apt/lists/*

# Create Wine prefix and set permissions
RUN mkdir -p /config/.wine && \
    chown -R abc:abc /config && \
    chmod -R 755 /config

# Download MT5
WORKDIR /config
RUN wget -O mt5setup.exe https://download.mql5.com/cdn/web/metaquotes.software.corp/mt5/mt5setup.exe

# Install MT5 silently (with dbus and check)
RUN xvfb-run -a -s "-screen 0 1024x768x24" sh -c " \
    dbus-uuidgen > /var/lib/dbus/machine-id && \
    wineboot --init && sleep 15 && \
    wine mt5setup.exe /silent && \
    wineserver -w && \
    sleep 5"

# Verify installation
RUN find /config/.wine -name "Terminal.exe" | grep "Terminal.exe" || (echo "MT5 Installation Failed!" && exit 1)

# Expose VNC port
EXPOSE 3000

# Default start
CMD ["/init"]