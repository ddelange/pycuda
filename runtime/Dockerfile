# this Dockerfile has just enough python to run your venv
ARG BASE_IMAGE
ARG BASE_TAG
FROM ${BASE_IMAGE}:${BASE_TAG}

ARG PYTHON_MINOR_VERSION
ARG PYTHON_DISTUTILS
RUN apt-install \
        python3.${PYTHON_MINOR_VERSION}${PYTHON_DISTUTILS} \
        libpython3.${PYTHON_MINOR_VERSION}-stdlib && \
    python -c "print('hello world')"
