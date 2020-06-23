FROM erlang:23

########################################
# docker-elixir 1.10.3

ENV ELIXIR_VERSION="v1.10.3" \
	LANG=C.UTF-8

RUN set -xe \
	&& ELIXIR_DOWNLOAD_URL="https://github.com/elixir-lang/elixir/archive/${ELIXIR_VERSION}.tar.gz" \
	&& ELIXIR_DOWNLOAD_SHA256="f3035fc5fdade35c3592a5fa7c8ee1aadb736f565c46b74b68ed7828b3ee1897" \
	&& curl -fSL -o elixir-src.tar.gz $ELIXIR_DOWNLOAD_URL \
	&& echo "$ELIXIR_DOWNLOAD_SHA256  elixir-src.tar.gz" | sha256sum -c - \
	&& mkdir -p /usr/local/src/elixir \
	&& tar -xzC /usr/local/src/elixir --strip-components=1 -f elixir-src.tar.gz \
	&& rm elixir-src.tar.gz \
	&& cd /usr/local/src/elixir \
	&& make install clean

########################################

ENV DEBCONF_NOWARNINGS yes

RUN apt-get update && \
    apt-get install -y build-essential automake autoconf git squashfs-tools ssh-askpass pkg-config curl && \
    rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/fhunleth/fwup/releases/download/v1.7.0/fwup_1.7.0_amd64.deb && \
    apt-get install -y ./fwup_1.7.0_amd64.deb && \
    rm ./fwup_1.7.0_amd64.deb && \
    rm -rf /var/lib/apt/lists/*

RUN apt-get update && \
    apt-get install -y expect && \
    rm -rf /var/lib/apt/lists/*

COPY mixlocalhex.exp /tmp
RUN expect /tmp/mixlocalhex.exp
RUN rm /tmp/mixlocalhex.exp

RUN mix local.rebar

COPY mixnervesbootstrap.exp /tmp
RUN expect /tmp/mixnervesbootstrap.exp
RUN rm /tmp/mixnervesbootstrap.exp

RUN ln -s fwup /usr/bin/fwup.exe

ENTRYPOINT /bin/bash
