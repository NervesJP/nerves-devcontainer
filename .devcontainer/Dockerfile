FROM elixir:1.9.4
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
