FROM python:3.8.6-slim-buster
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update \
	&& apt-get install -y xvfb chromium chromium-driver wget mc krb5-user libpam-krb5 gcc libkrb5-dev \
	&& rm -rf /var/lib/apt/lists/* \
	&& apt-get autoclean \
	&& apt-get clean \
	&& mkdir -p /etc/chromium/policies/recommended \
	&& touch /etc/chromium/policies/recommended/negotiate.json

COPY ./requirements.txt .
RUN pip3 install -r requirements.txt

CMD ["/bin/sh"]