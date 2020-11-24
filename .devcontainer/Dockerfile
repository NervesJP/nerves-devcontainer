FROM erlang:23.1.3

########################################
# docker-elixir 1.11.2
ENV ELIXIR_VERSION="v1.11.2" \
	LANG=C.UTF-8

RUN set -xe \
	&& ELIXIR_DOWNLOAD_URL="https://github.com/elixir-lang/elixir/archive/${ELIXIR_VERSION}.tar.gz" \
	&& ELIXIR_DOWNLOAD_SHA256="318f0a6cb372186b0cf45d2e9c9889b4c9e941643fd67ca0ab1ec32710ab6bf5" \
	&& curl -fSL -o elixir-src.tar.gz $ELIXIR_DOWNLOAD_URL \
	&& echo "$ELIXIR_DOWNLOAD_SHA256  elixir-src.tar.gz" | sha256sum -c - \
	&& mkdir -p /usr/local/src/elixir \
	&& tar -xzC /usr/local/src/elixir --strip-components=1 -f elixir-src.tar.gz \
	&& rm elixir-src.tar.gz \
	&& cd /usr/local/src/elixir \
	&& make install clean

########################################

ENV DEBCONF_NOWARNINGS yes

# Install dependent libraries for Nerves development
RUN apt-get update && \
    apt-get install -y build-essential automake autoconf git squashfs-tools ssh-askpass pkg-config curl && \
    rm -rf /var/lib/apt/lists/*

# Install fwup (https://github.com/fhunleth/fwup)
RUN wget https://github.com/fhunleth/fwup/releases/download/v1.8.2/fwup_1.8.2_amd64.deb && \
    apt-get install -y ./fwup_1.8.2_amd64.deb && \
    rm ./fwup_1.8.2_amd64.deb && \
    rm -rf /var/lib/apt/lists/*
# Make fake symbolic link for Windows environment
RUN ln -s fwup /usr/bin/fwup.exe

# Install hex and rebar
RUN mix local.hex --force
RUN mix local.rebar --force
# Install Mix environment for Nerves
RUN mix archive.install hex nerves_bootstrap --force

ENTRYPOINT /bin/bash
