FROM markrobinsonuzh/omni-docker:slim-py-3.11.7

USER root

COPY requirements.txt /tmp/
RUN pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    # trim some large things
    rm -rf /usr/local/lib/python3.11/site-packages/*/tests/* && \
    rm -rf /usr/local/lib/python3.11/site-packages/*/*/tests/*

USER omb
