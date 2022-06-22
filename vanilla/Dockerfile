# this Dockerfile demonstrates the creation of an empty venv in the runtime image
ARG PYTHON_MINOR_VERSION

FROM ghcr.io/ddelange/pycuda/buildtime:master as builder

ARG PYTHON_MINOR_VERSION

ENV VIRTUAL_ENV="/usr/share/python3/app"

ENV PATH="${VIRTUAL_ENV}/bin:${PATH}"

RUN python3.${PYTHON_MINOR_VERSION} -m venv ${VIRTUAL_ENV} && \
    pip install -U pip setuptools wheel


FROM ghcr.io/ddelange/pycuda/runtime:3.${PYTHON_MINOR_VERSION}-master

ENV VIRTUAL_ENV="/usr/share/python3/app"

ENV PATH="${VIRTUAL_ENV}/bin:${PATH}"

COPY --from=builder ${VIRTUAL_ENV} ${VIRTUAL_ENV}
