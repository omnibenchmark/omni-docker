FROM python:3.11-slim

ENV PATH /bin:/venv/bin:$PATH

# Create the user
RUN groupadd --gid 1000 omb && useradd --uid 1000 --gid 1000 -m omb

# Install basic packages
RUN python3 -m venv /venv && \
    apt update && apt install -y python3-dev gcc git git-annex && \
    pip3 install --no-cache-dir omnibenchmark && \
    # trim some large things
    rm -rf /usr/local/lib/python3.11/site-packages/*/tests/* && \
    rm -rf /usr/local/lib/python3.11/site-packages/*/*/tests/*

USER omb
