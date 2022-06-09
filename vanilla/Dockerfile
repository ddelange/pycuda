# this Dockerfile demonstrates the creation of an empty venv in the runtime image
ARG PYTHON_MINOR_VERSION

FROM ghcr.io/ddelange/pycuda/buildtime:master as builder

ARG PYTHON_MINOR_VERSION

ENV PATH="/usr/share/python3/app/bin:${PATH}"

RUN python3.${PYTHON_MINOR_VERSION} -m venv /usr/share/python3/app && \
    pip install -U pip setuptools wheel


FROM ghcr.io/ddelange/pycuda/runtime:3.${PYTHON_MINOR_VERSION}-master

ENV PATH="/usr/share/python3/app/bin:${PATH}"

COPY --from=builder /usr/share/python3/app /usr/share/python3/app
