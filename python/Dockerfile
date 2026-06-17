FROM python:3.12
LABEL org.opencontainers.image.source="https://github.com/peanutsguy/coder_images"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get upgrade -y && apt-get install -y git curl build-essential sudo

RUN curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
RUN apt-get install -y nodejs && node -v && npm -v

RUN rm -rf /var/lib/apt/lists/*

RUN useradd --groups sudo --create-home --shell /bin/bash coder --uid=1000 --user-group \
	&& echo "coder ALL=(ALL) NOPASSWD:ALL" >/etc/sudoers.d/coder \
	&& chmod 0440 /etc/sudoers.d/coder
USER coder
WORKDIR /home/coder