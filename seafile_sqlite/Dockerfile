FROM --platform=${BUILDPLATFORM} ubuntu
LABEL org.opencontainers.image.authors="jiri1@duck.com"
ENV SEAFILE_VERSION=9.0.2
ARG TARGETPLATFORM
SHELL ["/bin/bash", "-c"]

RUN apt-get update
RUN apt-get install -y wget python3 python3-setuptools python3-pip memcached libmemcached-dev pwgen sqlite3 python3-cffi
RUN pip3 install --timeout=3600 django==3.2.* future Pillow pylibmc captcha jinja2 psd-tools django-pylibmc django-simple-captcha pycryptodome==3.12.0


WORKDIR /seafile


RUN case ${TARGETPLATFORM} in \
         "linux/amd64")  DOWNLOAD=https://s3.eu-central-1.amazonaws.com/download.seadrive.org/seafile-server_${SEAFILE_VERSION}_x86-64.tar.gz  ;; \
         "linux/arm64")  DOWNLOAD=https://github.com/haiwen/seafile-rpi/releases/download/v${SEAFILE_VERSION}/seafile-server-${SEAFILE_VERSION}-focal-arm64v8l.tar.gz  ;; \
    esac \
 && wget -q $DOWNLOAD

RUN tar -xzf seafile-server*.tar.gz
RUN rm seafile-server*.tar.gz

# Clean up for smaller image
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN ulimit -n 30000

WORKDIR /seafile/seafile-server-${SEAFILE_VERSION}

CMD if [ -f /data/seahub.db ]; then \
        ln -s /data/ccnet /seafile \
        && ln -s /data/conf /seafile \
        && ln -s /data/logs /seafile \
        && ln -s /data/seahub-data /seafile \
        && ln -s /data/seafile-data /seafile \
        && ln -s /data/seahub.db /seafile \
        && bash -c "sed -i '/port\=/c\port\=${SEAFILE_PORT}' ../conf/seafile.conf" \
        && bash -c "sed -i '/bind \=/c\bind \= \"0.0.0.0:${SEAHUB_PORT}\"' ../conf/gunicorn.conf.py" \
        && bash seafile.sh start \
        && bash seahub.sh start ${SEAHUB_PORT} \
        && sleep infinity ; \
    else \
        bash -c "sed -i 's/= ask_admin_email()/= \"${SEAFILE_ADMIN}\"/' check_init_admin.py" \
        && bash -c "sed -i 's/= ask_admin_password()/= \"${SEAFILE_ADMIN_PW}\"/' check_init_admin.py" \
        && bash setup-seafile.sh auto -n seafile_server -i 0.0.0.0 -p ${SEAFILE_PORT} \
        && bash seafile.sh start \
        && bash seahub.sh start ${SEAHUB_PORT} \
        && bash seafile.sh stop \
        && bash seahub.sh stop \
        && mv /seafile/ccnet /data && ln -s /data/ccnet /seafile \
        && mv /seafile/conf /data && ln -s /data/conf /seafile \
        && mv /seafile/logs /data && ln -s /data/logs /seafile \
        && mv /seafile/seahub-data /data && ln -s /data/seahub-data /seafile \
        && mv /seafile/seafile-data /data && ln -s /data/seafile-data /seafile \
        && mv /seafile/seahub.db /data && ln -s /data/seahub.db /seafile \
        && bash -c "sed -i '/port\=/c\port\=${SEAFILE_PORT}' ../conf/seafile.conf" \
        && bash -c "sed -i '/bind \=/c\bind \= \"0.0.0.0:${SEAHUB_PORT}\"' ../conf/gunicorn.conf.py" \
        && bash seafile.sh start \
        && bash seahub.sh start ${SEAHUB_PORT} \
        && sleep infinity ; \
    fi

