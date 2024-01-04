FROM markrobinsonuzh/omni-docker:alpine-20231219--py-3.11

COPY requirements.txt /tmp/

RUN apk add --no-cache hdf5-dev && \
    pip install --upgrade pip setuptools wheel && \
    pip install -r /tmp/requirements.txt
