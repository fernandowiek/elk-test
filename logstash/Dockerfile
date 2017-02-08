FROM logstash:5.2.0 

COPY config/ /etc/logstash/conf.d/

RUN logstash-plugin install logstash-filter-json

CMD ["logstash" "-f" "/etc/logstash/conf.d/logstash.conf"]


