FROM python:3.12
LABEL org.opencontainers.image.source="https://github.com/peanutsguy/coder_images"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y git curl build-essential sudo podman
RUN curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg && mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg && sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/12/prod bookworm main" > /etc/apt/sources.list.d/dotnetdev.list' && apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y azure-functions-core-tools-4

RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

RUN rm -rf /var/lib/apt/lists/*

RUN useradd --groups sudo --create-home --shell /bin/bash coder --uid=1000 --user-group \
	&& echo "coder ALL=(ALL) NOPASSWD:ALL" >/etc/sudoers.d/coder \
	&& chmod 0440 /etc/sudoers.d/coder
USER coder
WORKDIR /home/coder