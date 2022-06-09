# this Dockerfile sets some defaults and prepares the toolchain
ARG BASE_IMAGE
ARG BASE_TAG
FROM ${BASE_IMAGE}:${BASE_TAG}

ENV TZ="Etc/UTC" \
	LANG="en_US.UTF-8" \
	DEBIAN_FRONTEND="noninteractive" \
	LC_CTYPE="en_US.UTF-8" \
	LC_NUMERIC="en_US.UTF-8" \
	LC_TIME="en_US.UTF-8" \
	LC_COLLATE="en_US.UTF-8" \
	LC_MONETARY="en_US.UTF-8" \
	LC_MESSAGES="en_US.UTF-8" \
	LC_PAPER="en_US.UTF-8" \
	LC_NAME="en_US.UTF-8" \
	LC_ADDRESS="en_US.UTF-8" \
	LC_TELEPHONE="en_US.UTF-8" \
	LC_MEASUREMENT="en_US.UTF-8" \
	LC_IDENTIFICATION="en_US.UTF-8"

# needed for string substitution in RUN directives
SHELL ["/bin/bash", "-c"]

COPY apt.conf /etc/apt/apt.conf.d/minimal
COPY tools/ /usr/local/sbin/

RUN apt-get update && \
	apt-get upgrade -y && \
	apt-get install -y gnupg2 openssl ca-certificates locales tzdata libffi7 apt-utils software-properties-common && \
	apt-get autoremove -y && \
	# add https://github.com/deadsnakes
	apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys F23C5A6CF475977595C89F51BA6932366A755776 && \
	add-apt-repository ppa:deadsnakes/ppa && \
	# configure locales
	ln -snf /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
	locale-gen en_US.UTF-8 && \
	dpkg-reconfigure locales tzdata && \
	# make tools executable
	chmod a+x /usr/local/sbin/* && \
	# cleanup
	rm -fr /var/lib/apt/lists /var/lib/cache/* /var/log/* /tmp/*
