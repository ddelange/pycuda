# this Dockerfile sets some defaults and prepares the toolchain
ARG BASE_IMAGE
ARG BASE_TAG
FROM ${BASE_IMAGE}:${BASE_TAG}

ENV TZ="Etc/UTC" \
	LANG="en_US.UTF-8" \
	LANGUAGE="en_US.UTF-8" \
	LC_ALL="en_US.UTF-8" \
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
	LC_IDENTIFICATION="en_US.UTF-8" \
	DEBIAN_FRONTEND="noninteractive" \
	SHELL="/bin/bash" \
	PIP_NO_CACHE_DIR=1 \
	UV_NO_CACHE=1 \
 	PYTHONDONTWRITEBYTECODE=1 \
 	PYTHONUNBUFFERED=1

# needed for string substitution in RUN directives
SHELL ["/bin/bash", "-c"]

COPY *.conf /etc/apt/apt.conf.d/
COPY tools/ /usr/local/sbin/

RUN apt-get update && \
	apt-get upgrade -y && \
	apt-get install -y gnupg2 openssl ca-certificates locales tzdata libffi7 apt-utils software-properties-common && \
	# add https://github.com/deadsnakes
	add-apt-repository ppa:deadsnakes/ppa && \
	# only needed to run the add-apt-repository python script
	apt-get purge -y software-properties-common && \
	# configure locales
	ln -snf /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
	locale-gen en_US.UTF-8 && \
	dpkg-reconfigure locales tzdata && \
	# make tools executable
	chmod a+x /usr/local/sbin/* && \
	# cleanup
	rm -fr /var/lib/apt/lists /var/lib/cache/* /var/log/* /tmp/*

# pre-activate venv, Docker style. ensure identical venv path in build stage and final stage, as venvs are not designed to be portable
ENV VIRTUAL_ENV="/usr/share/python3/app"
ENV PATH="${VIRTUAL_ENV}/bin:${PATH}"
