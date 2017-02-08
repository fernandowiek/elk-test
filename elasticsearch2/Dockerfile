FROM elasticsearch:5.2.0

ENV ES_JAVA_OPTS="-Des.path.conf=/etc/elasticsearch"

RUN elasticsearch-plugin install --batch x-pack

CMD ["-E", "discovery.zen.ping.unicast.hosts=elasticsearch"]

