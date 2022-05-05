FROM debian:stretch
MAINTAINER Damián Silvani <damian@dymaxionlabs.com>

ENV LANG=C.UTF-8 \
    DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        gdal-bin \
        libgdal-dev \
        libxmu6 \
        libxpm4 \
        libxt6 \
        musl \
        python \
        python-gdal \
        python-pyproj \
    && rm -rf /var/lib/apt/lists/*

ENV SEN2COR_VERSION 2.10.0
ENV SEN2COR_LONG_VERSION 02.10.01
ENV SEN2COR_DOWNLOAD_URL "http://step.esa.int/thirdparties/sen2cor/${SEN2COR_VERSION}/Sen2Cor-${SEN2COR_LONG_VERSION}-Linux64.run"
ENV SEN2COR_DOWNLOAD_HASH 91e846010e7aed9bfd1ee97336d2105d7a481f39ac133e2c4a4447d94251b9e7
ENV SEN2COR_INSTALLER "Sen2Cor.run"

RUN useradd -ms /bin/bash user && \
    mkdir /opt/sen2cor && \
    chown user /opt/sen2cor

USER user
WORKDIR /home/user

RUN curl -o ${SEN2COR_INSTALLER} -sSL ${SEN2COR_DOWNLOAD_URL} && \
    sha256sum $SEN2COR_INSTALLER && \
    echo "$SEN2COR_DOWNLOAD_HASH $SEN2COR_INSTALLER" | sha256sum -c - && \
    bash ${SEN2COR_INSTALLER} --target /opt/sen2cor && \
    rm ${SEN2COR_INSTALLER}

COPY src/entrypoint.sh /usr/local/bin

VOLUME /data

RUN unset LD_LIBRARY_PATH \
    unset PYTHONPATH \
    unset PYTHONHOME \
    unset PYTHONEXECUTABLE \
    unset PYTHONUSERBASE

ENV SEN2COR_PATH=/opt/sen2cor \
    SEN2COR_HOME=/home/user/sen2cor/2.10

ENV SEN2COR_BIN=$SEN2COR_PATH/lib/python2.7/site-packages/sen2cor \
    PATH=$SEN2COR_PATH/bin:${PATH} \
    LD_LIBRARY_PATH=$SEN2COR_PATH/lib:${LD_LIBRARY_PATH} \
    GDAL_DATA=$SEN2COR_PATH/share/gdal \
    GDAL_DRIVER_PATH=disable \
    LC_NUMERIC=C

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
