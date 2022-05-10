ARG BASE_IMAGE
ARG BASE_TAG
FROM ${BASE_IMAGE}:${BASE_TAG}

ENV PYTHONDONTWRITEBYTECODE=True \
	PIP_CONFIG_FILE=/etc/pip.conf

RUN apt-install \
		python3.6 python3.6-dev python3.6-venv \
		python3.7 python3.7-dev python3.7-venv \
		python3.8 python3.8-dev python3.8-venv \
		python3.9 python3.9-dev python3.9-venv \
		python3.10 python3.10-dev python3.10-venv python3.10-distutils \
		virtualenv \
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

# don't create cargo cache and fix the armv7 builds
RUN ln -snf /dev/shm /root/.cargo

ADD https://bootstrap.pypa.io/pip/3.6/get-pip.py /usr/share/python/get-pip-3.6.py
ADD https://bootstrap.pypa.io/get-pip.py /usr/share/python/get-pip.py

RUN \
	python3.6 /usr/share/python/get-pip-3.6.py pip setuptools wheel cython && \
	python3.7 /usr/share/python/get-pip.py pip setuptools wheel cython && \
	python3.8 /usr/share/python/get-pip.py pip setuptools wheel cython && \
	python3.9 /usr/share/python/get-pip.py pip setuptools wheel cython && \
	python3.10 /usr/share/python/get-pip.py pip setuptools wheel cython
