FROM ubuntu:focal

ARG PG_VERSION="12"
ARG BUCARDO_VERSION="5.6.0"

RUN apt-get -y update \
    && apt-get -y upgrade \
    && DEBIAN_FRONTEND=noninteractive apt-get -y install --no-install-recommends postgresql-${PG_VERSION} wget \
    && DEBIAN_FRONTEND=noninteractive apt-get -y install --no-install-recommends postgresql-plperl make \
    && DEBIAN_FRONTEND=noninteractive apt-get -y install --no-install-recommends libdbix-safe-perl libencode-locale-perl \
    && DEBIAN_FRONTEND=noninteractive apt-get -y install --no-install-recommends libcgi-pm-perl libdbd-pg-perl \
    && rm -rf /var/lib/apt/lists/*

RUN cd /tmp \
    && wget --no-check-certificate http://bucardo.org/downloads/Bucardo-${BUCARDO_VERSION}.tar.gz \
    && tar xzvf Bucardo-${BUCARDO_VERSION}.tar.gz \
    && cd Bucardo-${BUCARDO_VERSION} \
    && perl Makefile.PL \
    && make \
    && make install \
    && cd /tmp \
    && rm -rf Bucardo-${BUCARDO_VERSION}*


RUN printf '\
local   all             postgres                                peer\n\
local   bucardo         bucardo                                 md5\n\
local   all             all                                     peer\n\
host    all             all             127.0.0.1/32            md5\n\
host    all             all             ::1/128                 md5\n\
local   replication     all                                     peer\n\
host    replication     all             127.0.0.1/32            md5\n\
host    replication     all             ::1/128                 md5\n'\
> /etc/postgresql/${PG_VERSION}/main/pg_hba.conf

RUN printf '\
dbport = 5432\n\
dbuser = postgres\n\
dbname = bucardo\n' > /etc/bucardorc

RUN chown postgres /etc/postgresql/${PG_VERSION}/main/pg_hba.conf
RUN chown postgres /etc/bucardorc
RUN mkdir -p /var/log/bucardo && chown postgres /var/log/bucardo
RUN mkdir -p /var/run/bucardo && chown postgres /var/run/bucardo

RUN service postgresql start \
    && su - postgres -c "bucardo install --batch"

RUN printf '\
service postgresql start\n \
echo "Starting Bucardo..."\n \
su - postgres -c "bucardo start"\n \
while true; do\n \
    su - postgres -c "bucardo status"\n \
    sleep 5s\n \
done\n'  > /entrypoint.sh \
    && chmod +x /entrypoint.sh

ENTRYPOINT ["/bin/bash", "-c", "/entrypoint.sh"]
