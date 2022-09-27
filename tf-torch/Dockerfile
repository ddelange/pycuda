# this Dockerfile demonstrates the creation of a venv with CUDA-enabled libraries
ARG PYTHON_MINOR_VERSION

FROM ghcr.io/ddelange/pycuda/buildtime:master as builder

ARG PYTHON_MINOR_VERSION

ENV VIRTUAL_ENV="/usr/share/python3/app"

ENV PATH="${VIRTUAL_ENV}/bin:${PATH}"

# ref https://pytorch.org/get-started/locally/#linux-pip
ENV PIP_EXTRA_INDEX_URL="https://download.pytorch.org/whl/cu116"

RUN python3.${PYTHON_MINOR_VERSION} -m venv ${VIRTUAL_ENV} && \
    pip install -U pip setuptools wheel

RUN pip install jupyterlab ipywidgets tensorflow torch

RUN find-libdeps ${VIRTUAL_ENV} > ${VIRTUAL_ENV}/pkgdeps.txt


FROM ghcr.io/ddelange/pycuda/runtime:3.${PYTHON_MINOR_VERSION}-master

ENV VIRTUAL_ENV="/usr/share/python3/app"

ENV PATH="${VIRTUAL_ENV}/bin:${PATH}"

COPY --from=builder ${VIRTUAL_ENV} ${VIRTUAL_ENV}

RUN xargs -ra ${VIRTUAL_ENV}/pkgdeps.txt apt-install
