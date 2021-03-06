FROM docker.elastic.co/kibana/kibana-ubuntu-base:latest
MAINTAINER Elastic Docker Team <docker@elastic.co>
ARG KIBANA_DOWNLOAD_URL
ARG KIBANA_BASEPATH
EXPOSE 5601

WORKDIR /usr/share/kibana
RUN curl -Ls ${KIBANA_DOWNLOAD_URL} | tar --strip-components=1 -zxf - && \
    ln -s /usr/share/kibana /opt/kibana

# Set some Kibana configuration defaults.
ADD config/kibana.yml /usr/share/kibana/config/

# Add the launcher/wrapper script. It knows how to interpret environment
# variables and translate them to Kibana CLI options.
ADD bin/kibana-docker /usr/local/bin/

# Add a self-signed SSL certificate for use in examples.
ADD ssl/kibana.example.org.* /usr/share/kibana/config/

RUN usermod --home /usr/share/kibana kibana
USER kibana
ENV PATH=/usr/share/kibana/bin:$PATH

# Set server.basePath option and run kibana until optimization is complete
# This allows for faster startup times. Credit to https://github.com/elastic/kibana/issues/6057#issuecomment-254552969
ENV SERVER_BASEPATH=$KIBANA_BASEPATH
RUN bin/kibana --server.basePath=$SERVER_BASEPATH 2>&1 | grep -m 1 "Optimization of .* complete in .* seconds"

CMD /usr/local/bin/kibana-docker
