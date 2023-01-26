# this Dockerfile demonstrates the creation of a venv with CUDA-enabled libraries
ARG PYTHON_MINOR_VERSION

FROM ghcr.io/ddelange/pycuda/buildtime:master as builder

ARG PYTHON_MINOR_VERSION

RUN python3.${PYTHON_MINOR_VERSION} -m venv ${VIRTUAL_ENV} && \
    pip install -U pip setuptools wheel

# get CUDA wheel on x86, build from source on arm64 ref https://github.com/pytorch/pytorch/issues/13130
RUN pip install torch-1.13.1+cu117 --extra-index-url https://download.pytorch.org/whl/cu117 || \
    ( git clone --recurse-submodules --shallow-submodules --depth 1 --branch v1.13.1 https://github.com/pytorch/pytorch && BUILD_TEST=0 pip install -vv ./pytorch )

RUN find-libdeps ${VIRTUAL_ENV} > ${VIRTUAL_ENV}/pkgdeps.txt


FROM ghcr.io/ddelange/pycuda/runtime:3.${PYTHON_MINOR_VERSION}-master

COPY --from=builder ${VIRTUAL_ENV} ${VIRTUAL_ENV}

RUN xargs -ra ${VIRTUAL_ENV}/pkgdeps.txt apt-install
