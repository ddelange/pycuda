# this Dockerfile demonstrates the creation of a venv with CUDA-enabled libraries
ARG PYTHON_MINOR_VERSION

FROM ghcr.io/ddelange/pycuda/buildtime:master as builder

ARG PYTHON_MINOR_VERSION

ENV PATH="/usr/share/python3/app/bin:${PATH}" \
    # ref https://pytorch.org/get-started/locally/#linux-pip
    PIP_EXTRA_INDEX_URL="https://download.pytorch.org/whl/cu113"

RUN python3.${PYTHON_MINOR_VERSION} -m venv /usr/share/python3/app && \
    pip install -U pip setuptools wheel

RUN pip install distributed jupyterlab tensorflow torch

RUN find-libdeps /usr/share/python3/app > /usr/share/python3/app/pkgdeps.txt


FROM ghcr.io/ddelange/pycuda/runtime:3.${PYTHON_MINOR_VERSION}-master

ENV PATH="/usr/share/python3/app/bin:${PATH}"

COPY --from=builder /usr/share/python3/app /usr/share/python3/app

RUN xargs -ra /usr/share/python3/app/pkgdeps.txt apt-install
