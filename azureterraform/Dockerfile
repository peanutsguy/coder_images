FROM debian:13-slim
LABEL org.opencontainers.image.source="https://github.com/peanutsguy/coder_images"

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

RUN apt-get update && \
	apt-get upgrade -y && \
	apt-get install -y \
	apt-utils \
	curl \
	git \
	sudo \
	vim \
	wget \
	unzip \
	jq \
	apt-transport-https \
	gnupg \
	lsb-release \
	procps

RUN wget -O - https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list

RUN apt-get update && apt-get install -y terraform

# Download and modify the install script to use a hardcoded path instead of HOME-based path
RUN curl -fsSL https://opencode.ai/install -o /tmp/opencode_install.sh && \
    sed -i 's|INSTALL_DIR=\$HOME/.opencode/bin|INSTALL_DIR=/usr/local/bin|g' /tmp/opencode_install.sh && \
    chmod +x /tmp/opencode_install.sh && \
    /tmp/opencode_install.sh && \
    rm -f /tmp/opencode_install.sh && \
    # Verify installation location
    which opencode | grep -q /usr/local/bin/opencode

RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

RUN apt-get purge -y --auto-remove && \ 
	apt-get clean && \ 
	rm -rf /var/lib/apt/lists/*

COPY bin/* /usr/local/bin/
WORKDIR /usr/local/bin
RUN chmod +x tfcheck module gittar

RUN useradd --groups sudo --create-home --shell /bin/bash coder --uid=1000 --user-group \
	&& echo "coder ALL=(ALL) NOPASSWD:ALL" >/etc/sudoers.d/coder \
	&& chmod 0440 /etc/sudoers.d/coder
USER coder
WORKDIR /home/coder