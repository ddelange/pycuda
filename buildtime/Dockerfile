# this Dockerfile has multiple python versions and common build tools
ARG BASE_IMAGE
ARG BASE_TAG
FROM ${BASE_IMAGE}:${BASE_TAG}

ENV PYTHONDONTWRITEBYTECODE=True

RUN apt-install \
		python3.6 python3.6-dev python3.6-venv \
		python3.7 python3.7-dev python3.7-venv \
		python3.8 python3.8-dev python3.8-venv \
		python3.9 python3.9-dev python3.9-venv \
		python3.10 python3.10-dev python3.10-venv python3.10-distutils \
		python3.11 python3.11-dev python3.11-venv python3.11-distutils \
		# useful headers etc
		cargo \
		rustc \
		libffi-dev \
		libmysqlclient-dev \
		libpq-dev \
		libssl-dev \
		libxml2-dev \
		libxslt1-dev \
		libyaml-dev \
		git-core \
		build-essential
