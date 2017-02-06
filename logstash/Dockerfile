FROM logstash:5.2.0 

# Add your logstash plugins setup here

COPY config/logstash.conf /etc/logstash/conf.d/logstash.conf
#RUN logstash-plugin install  x-pack


CMD ["logstash" "-f" "/etc/logstash/conf.d/logstash.conf"]
#RUN logstash-plugin install logstash-filter-json


