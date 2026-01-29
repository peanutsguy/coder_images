FROM debian:13-slim
LABEL org.opencontainers.image.source="https://github.com/peanutsguy/coder_images"

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

RUN apt-get update && \
	apt-get upgrade -y && \
	apt-get install -y \
	software-properties-common \
	build-essential \
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

RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

RUN curl -SL https://deb.nodesource.com/setup_22.x | bash
RUN apt-get update && apt-get install -y nodejs

RUN npm install -g @azure/static-web-apps-cli

RUN rm -rf /var/lib/apt/lists/*

RUN useradd --groups sudo --create-home --shell /bin/bash coder --uid=1000 --user-group \
	&& echo "coder ALL=(ALL) NOPASSWD:ALL" >/etc/sudoers.d/coder \
	&& chmod 0440 /etc/sudoers.d/coder
USER coder
WORKDIR /home/coder