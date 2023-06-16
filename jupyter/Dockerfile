# this Dockerfile demonstrates the creation of a venv with a jupyter server
ARG PYTHON_MINOR_VERSION

FROM ghcr.io/ddelange/pycuda/buildtime:master as builder

ARG PYTHON_MINOR_VERSION

RUN python3.${PYTHON_MINOR_VERSION} -m venv ${VIRTUAL_ENV} && \
    pip install -U pip setuptools wheel

RUN pip install jupyterlab 'jupyter_collaboration ; python_version >= "3.8"' ipywidgets ipdb

RUN find-libdeps ${VIRTUAL_ENV} > ${VIRTUAL_ENV}/pkgdeps.txt


FROM ghcr.io/ddelange/pycuda/runtime:3.${PYTHON_MINOR_VERSION}-master

COPY --from=builder ${VIRTUAL_ENV} ${VIRTUAL_ENV}

RUN xargs -ra ${VIRTUAL_ENV}/pkgdeps.txt apt-install
