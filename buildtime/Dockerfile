ARG BASE_IMAGE
ARG BASE_TAG
FROM ${BASE_IMAGE}:${BASE_TAG}

ENV PYTHONDONTWRITEBYTECODE=True

# Install headers for useful libraries
RUN apt-install git-core build-essential
RUN apt-install virtualenv python python-dev python-setuptools python-wheel-common

ADD https://bootstrap.pypa.io/pip/2.7/get-pip.py /usr/share/python/get-pip2.py
RUN python /usr/share/python/get-pip2.py "pip < 21.0" "setuptools < 50.0" "wheel < 1.0"
RUN python2 -m pip wheel -w /usr/share/python-wheels "pip<21.0" "setuptools<50.0" "wheel<1.0"

ADD https://bootstrap.pypa.io/pip/3.6/get-pip.py /usr/share/python/get-pip-3.6.py
ADD https://bootstrap.pypa.io/get-pip.py /usr/share/python/get-pip.py
RUN apt-install \
	python3.8 \
	python3.8-dev \
	python3-setuptools \
	python3-wheel \
	python3-venv \
	python3-virtualenv \
	virtualenv

RUN apt-install -y python2.7 python2.7-dev
RUN apt-install -y python3.6 python3.6-dev python3.6-venv
RUN apt-install -y python3.7 python3.7-dev python3.7-venv
RUN apt-install -y python3.9 python3.9-dev python3.9-venv
RUN apt-install -y python3.10 python3.10-dev python3.10-venv python3.10-distutils

RUN apt-install \
	cargo \
	libffi-dev \
	libmysqlclient-dev \
	libpq-dev \
	libssl-dev \
	libxml2-dev \
	libxslt1-dev \
	libyaml-dev \
	rustc

# No create cargo cache and fixing the armv7 builds
RUN ln -snf /dev/shm /root/.cargo

RUN \
	python3.6 /usr/share/python/get-pip-3.6.py "pip == 21.2.4" "setuptools < 60" "wheel == 0.37.0" "cython" && \
	python3.7 /usr/share/python/get-pip.py "pip == 21.2.4" "setuptools < 60" "wheel == 0.37.0" "cython" && \
	python3.8 /usr/share/python/get-pip.py "pip == 21.2.4" "setuptools < 60" "wheel == 0.37.0" "cython" && \
	python3.9 /usr/share/python/get-pip.py "pip == 21.2.4" "setuptools < 60" "wheel == 0.37.0" "cython" && \
	python3.10 /usr/share/python/get-pip.py "pip == 21.2.4" "setuptools < 60" "wheel == 0.37.0" "cython"

# Upgrade preinstalled wheels
RUN rm -fr \
	/usr/share/python-wheels/pip*.whl \
	/usr/share/python-wheels/wheel*.whl \
	/usr/share/python-wheels/setuptools*.whl \
	&& python3 -m pip wheel --cache-dir /tmp/wheels -w /usr/share/python-wheels --no-binary=:all: \
		"pip==21.2.4" "wheel==0.37.0" "setuptools<60" \
	&& rm -fr /tmp/wheels

RUN python3 -m venv /usr/share/python3/tox && \
	/usr/share/python3/tox/bin/pip install tox && \
	ln -snf /usr/share/python3/tox/bin/tox /usr/bin/tox && \
	ln -snf /usr/share/python3/tox/bin/tox /usr/local/bin/tox

RUN python3 -m venv /usr/share/python3/coveralls && \
	/usr/share/python3/coveralls/bin/pip install coveralls && \
	ln -snf /usr/share/python3/coveralls/bin/coveralls /usr/bin/coveralls && \
	ln -snf /usr/share/python3/coveralls/bin/coveralls /usr/local/bin/coveralls

ENV PIP_CONFIG_FILE /etc/pip.conf

RUN mkdir -p /var/cache/pip
ENV PIP_DOWNLOAD_CACHE=/var/cache/pip