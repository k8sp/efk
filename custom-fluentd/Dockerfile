FROM yancey1989/fluentd-base:0.12.29
MAINTAINER yancey1989@gmail.com
WORKDIR /home/fluent
ENV PATH /home/fluent/.gem/ruby/2.3.0/bin:$PATH

USER root
COPY fluent.conf /fluentd/etc/

EXPOSE 24284

CMD exec fluentd -c /fluentd/etc/$FLUENTD_CONF -p /fluentd/plugins $FLUENTD_OPT
