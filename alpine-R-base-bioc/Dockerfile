FROM markrobinsonuzh/omni-docker:alpine-20231219--R-4.3.2

COPY install.R /tmp/
RUN apk --no-cache add \
    imagemagick imagemagick-dev && \
    R -f /tmp/install.R && \
    R --version

