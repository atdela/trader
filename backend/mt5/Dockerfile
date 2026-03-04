# Stage 1: Base image
FROM ghcr.io/linuxserver/baseimage-kasmvnc:debianbullseye-8446af38-ls104 AS base

ENV DEBIAN_FRONTEND=noninteractive
ENV TITLE=MetaTrader
ENV WINEARCH=win64
ENV WINEPREFIX="/config/.wine"
ENV DISPLAY=:0

# Create wine directory with correct permissions
RUN mkdir -p /config/.wine && \
    chown -R abc:abc /config/.wine && \
    chmod -R 755 /config/.wine

# 🔥 DO NOT UPGRADE — only update
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    dos2unix \
    python3-pip \
    wget \
    python3-pyxdg \
    netcat \
    gnupg2 \
    ca-certificates \
    software-properties-common \
    && rm -rf /var/lib/apt/lists/*

# Upgrade pip safely
RUN pip3 install --no-cache-dir --upgrade pip

# Add WineHQ key safely (modern method, no apt-key)
RUN wget -qO /usr/share/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key

# Add WineHQ repository
RUN echo "deb [signed-by=/usr/share/keyrings/winehq-archive.key] https://dl.winehq.org/wine-builds/debian/ bullseye main" \
    > /etc/apt/sources.list.d/winehq.list

# Enable i386 architecture
RUN dpkg --add-architecture i386 && apt-get update

# Install Wine stable
RUN apt-get install -y --install-recommends winehq-stable && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Stage 2: Final image
FROM base

COPY app /app
COPY scripts /scripts

RUN dos2unix /scripts/*.sh && \
    chmod +x /scripts/*.sh

COPY root /

RUN touch /var/log/mt5_setup.log && \
    chown abc:abc /var/log/mt5_setup.log && \
    chmod 644 /var/log/mt5_setup.log

EXPOSE 3000 5000 5001 8001 18812
VOLUME /config