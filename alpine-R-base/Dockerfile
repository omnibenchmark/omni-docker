FROM alpine:20231219

# Note: used code from https://gitlab.com/artemklevtsov/r-alpine/-/blob/master/Dockerfile?ref_type=heads
MAINTAINER Mark Robinson mark.robinson@mls.uzh.ch

ARG R_VERSION
ENV R_VERSION ${R_VERSION:-4.3.2}
ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV CRAN https://cran.r-project.org
ENV R_DAILY_URL https://stat.ethz.ch/R/daily
ENV PATH /bin:/venv/bin:$PATH 


    # R runtime dependencies
RUN apk --no-cache add \
        linux-headers \
        gcc \
        gfortran \
        musl-locales \
        g++ \
        make \
        readline-dev \
        icu-dev \
        zlib-dev \
        bzip2-dev \
        xz-dev \
        pcre-dev \
        pcre2-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        tiff-dev  \
        curl-dev \
        openblas-dev \
        pango-dev \
        libxml2 libxml2-dev \
        cairo-dev \
        git git-annex \
        zip \
        file \
        coreutils \
        bash  && \
    # build dependencies
    apk --no-cache add --virtual build-deps \
        perl \
        openjdk8-jre-base \
        tcl-dev \
        python3-dev \
        py3-pip \
        tk-dev && \
    # renku-python
    python3 -m venv /venv && \
    pip3 install omnibenchmark && \
    cd /tmp && \
    # Download source code
    if [[ "${R_VERSION}" == "devel" || "${R_VERSION}" == "patched" ]]; then \
        R_SRC_URL=${R_DAILY_URL}; \
    else \
        R_SRC_URL=${CRAN}/src/base/R-${R_VERSION%%.*}; \
    fi && \
    wget ${R_SRC_URL}/R-${R_VERSION}.tar.gz && \
    # Extract source code
    tar -xf R-${R_VERSION}.tar.gz && \
    if [ -d R-beta ]; then mv R-beta R-patched; fi && \
    if [ -d R-rc ]; then mv R-rc R-patched; fi && \
    cd R-${R_VERSION} && \
    # Set compiler flags
    CFLAGS="-g -O2 -fstack-protector-strong -D_DEFAULT_SOURCE -D__USE_MISC" \
    CXXFLAGS="-g -O2 -fstack-protector-strong -D_FORTIFY_SOURCE=2 -D__MUSL__" \
    # configure script options
    ./configure --prefix=/usr \
                --sysconfdir=/etc/R \
                --localstatedir=/var \
                rdocdir=/usr/share/doc/R \
                rincludedir=/usr/include/R \
                rsharedir=/usr/share/R \
                --enable-memory-profiling \
                --enable-R-shlib \
                --enable-java \
                --with-x=no \
                --disable-nls \
                #--without-recommended-packages && \
                --with-blas=openblas && \
    # Build and install R
    make -j $(nproc) && \
    make install && \
    cd src/nmath/standalone && \
    make -j $(nproc) && \
    make install && \
    rm -f /usr/lib/R/bin/R && \
    ln -s /usr/bin/R /usr/lib/R/bin/R && \
    # Fix library path
    echo "R_LIBS_SITE=\${R_LIBS_SITE-'/usr/lib/R/library'}" >> /usr/lib/R/etc/Renviron && \
    # Add default CRAN mirror
    echo "options(repos = c(CRAN = '${CRAN}'))" >> /usr/lib/R/etc/Rprofile.site && \
    # Add symlinks for the config ifile in /etc/R
    mkdir -p /etc/R && \
    ln -s /usr/lib/R/etc/* /etc/R/ && \
    # Add library directory
    mkdir -p /usr/lib/R/site-library && \
    chgrp users /usr/lib/R/site-library && \
    # Remove build dependencies
    apk del --purge --rdepends build-deps && \
    # Strip libs
    strip -x /usr/lib/R/bin/exec/R && \
    strip -x /usr/lib/R/lib/* && \
    find /usr/lib/R -name "*.so" -exec strip -x {} \; && \
    # Clean up
    rm -rf /R-${R_VERSION}* && \
    rm -rf /usr/lib/R/library/translations && \
    rm -rf /usr/lib/R/doc && \
    mkdir -p /usr/lib/R/doc/html && \
    mkdir -p /usr/share/doc/R && \
    mkdir -p /usr/share/doc/R/html && \
    touch /usr/lib/R/doc/html/R.css && \
    touch /usr/share/doc/R/html/R.css && \
    # trim some large things
    rm -rf /usr/lib/R/*/*/help/*.rdb && \
    rm -rf /var/cache/apk/* && \
    rm -rf /usr/bin/lto-dump && \
    rm -rf /root/.cache/ && \
    # remove some duplicates
    rm -rf /usr/bin/x86_64-alpine-linux-musl-gcc && \
    rm -rf /usr/bin/x86_64-alpine-linux-musl-gcc-13.2.1 && \
    ln -s /usr/bin/gcc /usr/bin/x86_64-alpine-linux-musl-gcc-13.2.1 && \
    rm -rf /usr/bin/x86_64-alpine-linux-musl-gfortran && \
    ln -s /usr/bin/gfortran /usr/bin/x86_64-alpine-linux-musl-gfortran && \
    rm -rf /usr/bin/g++ /usr/bin/x86_64-alpine-linux-musl-c++ /usr/bin/x86_64-alpine-linux-musl-g++ && \
    ln -s /usr/bin/c++ /usr/bin/g++ && \
    ln -s /usr/bin/c++ /usr/bin/x86_64-alpine-linux-musl-c++ && \
    ln -s /usr/bin/c++ /usr/bin/x86_64-alpine-linux-musl-g++ && \
    rm -rf /usr/bin/ld.bfd && \
    ln -s /usr/bin/ld /usr/bin/lb.bfd && \
    rm -rf /usr/x86_64-alpine-linux-musl/bin/as && \
    rm -rf /usr/x86_64-alpine-linux-musl/bin/ld && \
    rm -rf /tmp/* && \
    # log some versions
    R --vanilla -e 'capabilities()' && \
    apk info -v 2> /dev/null | grep -e lapack -e blas -e zlib && \
    echo "renku version: $(renku --version)" && \
    echo "omnibenchmark version: $(pip freeze | grep omnib)" && \
    # more clean-up
    rm -rf /usr/share/xml/docbook && \
    rm -rf /usr/share/doc/R && \
    rm -rf /venv/lib/python3.11/site-packages/pip  # clunky but 

# Create the user
RUN adduser -D -g omb omb
USER omb

ENV LANG C.UTF-8
ENV LANGUAGE C.UTF-8 
ENV LC_ALL C.UTF-8

