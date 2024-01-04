FROM markrobinsonuzh/omni-docker:alpine-20231219--py-3.11

COPY requirements.txt /tmp/

RUN apk add --no-cache hdf5-dev && \
    pip install --upgrade pip setuptools wheel && \
    pip install -r /tmp/requirements.txt && \
    apk --no-cache --purge del build-base && \
    pip uninstall --yes setuptools wheel && \
    # trim some large things
    rm -rf /var/cache/apk/* && \
    rm -rf /usr/bin/lto-dump && \
    rm -rf /usr/local/lib/python3.11/site-packages/*/tests/* && \
    rm -rf /usr/local/lib/python3.11/site-packages/*/*/tests/*

