FROM resin/rpi-raspbian:latest

ENV METRICBEAT_VERSION 5.2
ENV GOPATH /go
ENV GOROOT /usr/local/go
RUN apt-get update \
	&& apt-get install -y \
	curl \
	git \
	golang \
	build-essential \
	python \
	python-pip \
	&& apt-get clean


RUN mkdir -p ${GOPATH}/src/github.com/elastic
RUN mkdir -p ${GOPATH}/bin
ENV PATH=${GOPATH}/bin:${GOROOT}/bin:$PATH
WORKDIR ${GOPATH}/src/github.com/elastic
RUN git clone https://github.com/elastic/beats.git
WORKDIR ${GOPATH}/src/github.com/elastic/beats/metricbeat
RUN curl https://glide.sh/get | sh
RUN glide up
RUN make
RUN pip install virtualenv
RUN make update
RUN make install

ENV PATH=/usr/share/metricbeat:$PATH

COPY config/metricbeat.yml /usr/share/metricbeat/

# Provide a non-root user to run the process.
RUN chown --recursive 1000:1000 /usr/share/metricbeat && \
    addgroup --gid 1000 metricbeat && \
    adduser --disabled-password --gecos=metricbeat --uid=1000 --gid=1000 --home /usr/share/metricbeat metricbeat
USER metricbeat
WORKDIR /usr/share/metricbeat

CMD ["metricbeat", "-e", "-v"]