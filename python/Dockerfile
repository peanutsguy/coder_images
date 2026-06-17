FROM python:3.12
LABEL org.opencontainers.image.source="https://github.com/peanutsguy/coder_images"

ENV DEBIAN_FRONTEND=noninteractive

# Package update and essentials installation
RUN apt-get update && apt-get upgrade -y && apt-get install -y git curl build-essential sudo ca-certificates

# Install NodeJS24
RUN curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
RUN apt-get install -y nodejs && node -v && npm -v

# Install Docker CLI - Engine not included
RUN install -m 0755 -d /etc/apt/keyrings && curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc && chmod a+r /etc/apt/keyrings/docker.asc

RUN tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

RUN apt-get update && apt-get install -y docker-ce-cli docker-buildx-plugin docker-compose-plugin

#Cleanup
RUN rm -rf /var/lib/apt/lists/*

# Coder user config
RUN useradd --groups sudo --create-home --shell /bin/bash coder --uid=1000 --user-group \
	&& echo "coder ALL=(ALL) NOPASSWD:ALL" >/etc/sudoers.d/coder \
	&& chmod 0440 /etc/sudoers.d/coder
USER coder
WORKDIR /home/coder
