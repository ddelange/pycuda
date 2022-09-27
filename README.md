# pycuda

Build compact multi-stage CUDA images for Python based ML projects.


## Concept

The main idea of this method is to create a `virtualenv` for your package using a
heavy full-powered image (which contains commonly 
used headers, libraries, compilers, etc. which are only needed during build time), and then copy it into a
base image with a Python version of choice.
Any build artifacts that are unneeded for runtime won't be copied into the final image, further reducing image size.

## Reasoning

Why so complex? You could just `COPY` directory with your python project into 
Docker container, and for the first point of view this seems to be reasonable. 

But just copying a directory with a python project can cause several problems:

- Generated on different operating system .pyc files can be put into Docker 
  image accidentally. Thus, python would try to rewrite .pyc with correct ones 
  each time when Docker image would be started. If you would run Docker image 
  in read-only mode - your application would break.  
   
- Large possibility that you would also pack garbage files: pytest and tox 
  cache, developer's virtualenv and other files, that just increase the size of 
  the resulting image.

- No explicit entrypoint. It is not obvious what commands end user is able to 
  run (we hope you've implemented `-h` or `--help` arguments).

- By default, tox interprets your package as python module, e.g. it tries to 
  run `pip install .` when preparing environment.

Yes, of course, you can solve all of those problems using hacks, specific
settings, .dockerignore file, and other tricks. But it would be non-intuitive 
and non-obvious for your users.

So, we recommend to spend a little more time and pack your package carefully, 
so your users would run it with pleasure.

## Usage

For example, you may build a jupyter notebook server with Tensorflow pre-installed. Just create a Dockerfile 
with the following content:

```Dockerfile
#################################################################
####################### BUILD STAGE #############################
#################################################################
# This image contains:
# 1. Multiple Python versions
# 2. Required python headers
# 3. C compiler and developer tools
FROM ghcr.io/ddelange/pycuda/buildtime:master as builder

# Create virtualenv on e.g. python 3.9
# Target folder should be the same on the build stage and on the target stage
# A VIRTUAL_ENV variable is set in the shared base image to make this easier
RUN python3.9 -m venv ${VIRTUAL_ENV} && \
    pip install -U pip setuptools wheel

# Install some packages into the venv
RUN pip install jupyterlab ipywidgets ipdb tensorflow

# Record the required system libraries/packages
RUN find-libdeps ${VIRTUAL_ENV} > ${VIRTUAL_ENV}/pkgdeps.txt

#################################################################
####################### TARGET STAGE ############################
#################################################################
# Use the same python version used on the build stage
FROM ghcr.io/ddelange/pycuda/runtime:3.9-master

# Copy over the venv (ensure same path as venvs are not designed to be portable)
COPY --from=builder ${VIRTUAL_ENV} ${VIRTUAL_ENV}

# Install the required library packages
RUN xargs -ra ${VIRTUAL_ENV}/pkgdeps.txt apt-install

# The packages in the venv are now ready to use
ENV PORT=1337
CMD ["jupyter", "lab", "--no-browser", "--port=${PORT}"]
```

And just build this:
```bash
docker build -t jupyter .
```

There are also pre-built jupyter images [available](https://github.com/ddelange/pycuda/pkgs/container/pycuda%2Fjupyter).

## Useful tools

All images contain ready to use and simple wrappers for easy image building.

### apt-install

Pretty simple bash script. The main purpose is removing the apt cache and temporary files after installation when you want to install something through apt-get install.

Otherwise, you have to write something like this 

```bash
apt-get update && \
apt-get install -y --no-upgrade --auto-remove tcpdump && \
rm -fr /var/lib/apt/lists /var/lib/cache/* /var/log/*
```

It might be replaced like this:
```bash
apt-install tcpdump
```

The `--no-upgrade` flag avoids build failures when passing held packages (like cudnn8 installed and held in the cuda base image), e.g. originating from the `find-libdeps` command below.

### wait-for-port

Python script which waits for availability one or multiple TCP ports. It's very useful for tests and with docker-compose.

```bash
wait-for-port --period=0.5 --timeout=600 postgres:5432 pgbouncer:6432 && python myscript.py
```
Or shorter (values from previous example are defaults):
```bash
wait-for-port postgres:5432 pgbouncer:6432 && python myscript.py
```

This script will be trying to make connections to passed endpoints until timeout would be reached or endpoints stay connectable.


### find-libdeps

A shell script which find binary `*.so` files and resolve required system package for install library dependencies.

Save required packages
```bash
find-libdeps /usr/share/python3/app > /usr/share/python3/app/pkgdeps.txt
```

Install saved packages
```bash
xargs -ra /usr/share/python3/app/pkgdeps.txt apt-install
```

