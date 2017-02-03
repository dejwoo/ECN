FROM resin/rpi-raspbian:latest

ARG ELASTIC_VERSION
ARG DOWNLOAD_URL

ENV METRICBEAT_VERSION 5.2

RUN apt-get update \
	&& apt-get install -y \
	curl \
	git \
	golang \
	build-essentials \
	&& apt-get clean


RUN mkdir -p ${GOPATH}/src/github.com/elastic
RUN cd ${GOPATH}/src/github.com/elastic
RUN git clone https://github.com/elastic/beats.git


RUN curl -Lso - ${DOWNLOAD_URL} | \
    tar zxf - -C /usr/share && \
    mv /usr/share/metricbeat-5.2.0-linux-x86_64 /usr/share/metricbeat
ENV PATH=/usr/share/metricbeat:$PATH

COPY config/metricbeat.yml /usr/share/metricbeat/

# Provide a non-root user to run the process.
RUN chown --recursive 1000:1000 /usr/share/metricbeat && \
    addgroup --gid 1000 metricbeat && \
    adduser --disabled-password --gecos=metricbeat --uid=1000 --gid=1000 --home /usr/share/metricbeat metricbeat
USER metricbeat
WORKDIR /usr/share/metricbeat

CMD ["metricbeat", "-e", "-v"]