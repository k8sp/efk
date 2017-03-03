FROM alpine:3.4
MAINTAINER Yancey <yancey1989@gmail.com>
LABEL Description="Fluentd docker image" Vendor="Fluent Organization" Version="1.1"

# Do not split this into multiple RUN!
# Docker creates a layer for every RUN-Statement
# therefore an 'apk delete build*' has no effect
RUN apk --no-cache --update add \
                            build-base \
                            ca-certificates \
                            ruby \
                            ruby-irb \
                            ruby-dev \
                            openssl && \
    echo 'gem: --no-document' >> /etc/gemrc && \
    gem install oj && \
    gem install json && \
    gem install fluentd -v 0.12.29 && \
    wget -O /tmp/jemalloc-4.3.0.tar.bz2 https://github.com/jemalloc/jemalloc/releases/download/4.3.0/jemalloc-4.3.0.tar.bz2 && \
    cd /tmp && tar -xjf jemalloc-4.3.0.tar.bz2  && cd jemalloc-4.3.0/ && \
    ./configure && make && \
    mv lib/libjemalloc.so.2 /usr/lib && cd / 

RUN adduser -D -g '' -u 1000 -h /home/fluent fluent
RUN chown -R fluent:fluent /home/fluent

# for log storage (maybe shared with host)
RUN mkdir -p /fluentd/log
# configuration/plugins path (default: copied from .)
RUN mkdir -p /fluentd/etc /fluentd/plugins

RUN chown -R fluent:fluent /fluentd

USER fluent
WORKDIR /home/fluent

# Tell ruby to install packages as user
RUN echo "gem: --user-install --no-document" >> ~/.gemrc
ENV PATH /home/fluent/.gem/ruby/2.3.0/bin:$PATH
ENV GEM_PATH /home/fluent/.gem/ruby/2.3.0:$GEM_PATH

USER root
RUN apk --no-cache --update add sudo build-base ruby-dev && \

    sudo -u fluent gem install fluent-plugin-secure-forward && \
    sudo -u fluent gem install fluent-plugin-elasticsearch && \
    sudo -u fluent gem install fluent-plugin-kubernetes && \

    rm -rf /home/fluent/.gem/ruby/2.3.0/cache/*.gem && sudo -u fluent gem sources -c && \
    apk del sudo build-base ruby-dev && rm -rf /var/cache/apk/*

# Environment variables
ENV FLUENTD_OPT=""
ENV FLUENTD_CONF="fluent.conf"
# ENV LD_PRELOAD="/usr/lib/libjemalloc.so.2"
