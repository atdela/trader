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
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    wget \
    wine64 \
    wine32 \
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
    && rm -rf /var/lib/apt/lists/*

# Create Wine prefix
RUN mkdir -p /config/.wine

# Download MT5
WORKDIR /config
RUN wget -O mt5setup.exe https://download.mql5.com/cdn/web/metaquotes.software.corp/mt5/mt5setup.exe

# Install MT5 silently
RUN wineboot --init && \
    sleep 5 && \
    wine mt5setup.exe /silent

# Expose VNC port
EXPOSE 3000

# Default start
CMD ["/init"]