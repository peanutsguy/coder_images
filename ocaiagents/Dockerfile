FROM debian:13-slim
LABEL org.opencontainers.image.source="https://github.com/peanutsguy/coder_images"

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

RUN apt-get update && \
	apt-get upgrade -y && \
	apt-get install -y \
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

RUN curl -fsSL https://deb.nodesource.com/setup_24.x | bash -
RUN apt-get install -y nodejs

RUN curl -LsSf https://astral.sh/uv/install.sh | sh

RUN rm -rf /var/lib/apt/lists/*

RUN useradd --groups sudo --create-home --shell /bin/bash coder --uid=1000 --user-group \
	&& echo "coder ALL=(ALL) NOPASSWD:ALL" >/etc/sudoers.d/coder \
	&& chmod 0440 /etc/sudoers.d/coder
USER coder
WORKDIR /home/coder

RUN curl -fsSL https://opencode.ai/install | bash

RUN npx -y oh-my-opencode install --no-tui --claude=no --gemini=no --copilot=no