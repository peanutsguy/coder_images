FROM ubuntu:22.04
LABEL org.opencontainers.image.source="https://github.com/peanutsguy/coder_images"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
	apt-utils \
	curl \
	git \
	sudo \
	vim \
	wget \
	unzip \
	jq \
	apt-transport-https \
	software-properties-common

RUN curl --proto '=https' --tlsv1.2 -fsSL https://get.opentofu.org/install-opentofu.sh -o install-opentofu.sh && chmod +x install-opentofu.sh && ./install-opentofu.sh --install-method deb && rm install-opentofu.sh

RUN wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | tee /usr/share/keyrings/trivy.gpg > /dev/null
RUN echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | tee -a /etc/apt/sources.list.d/trivy.list

RUN apt-get update && apt-get install -y trivy

RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

RUN rm -rf /var/lib/apt/lists/*

RUN ln -s /usr/bin/tofu /usr/local/bin/terraform

COPY bin/* /usr/local/bin/
WORKDIR /usr/local/bin
RUN chmod +x tfcheck module gittar

RUN useradd --groups sudo --create-home --shell /bin/bash coder --uid=1000 --user-group \
	&& echo "coder ALL=(ALL) NOPASSWD:ALL" >/etc/sudoers.d/coder \
	&& chmod 0440 /etc/sudoers.d/coder
USER coder
WORKDIR /home/coder