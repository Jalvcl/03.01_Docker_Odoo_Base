FROM debian:stretch
LABEL io.github.jalvcl.maintainer="jalv jlvzenzey@yahoo.com"
LABEL io.github.jalvcl.version="11p.01"
LABEL io.github.jalvcl.repository="https://github.com/Jalvcl/03.01_Docker_Odoo_Base"
LABEL io.github.jalvcl.description="Odoo 11 for further development, with minor modificatons"
LABEL io.github.jalvcl.based_on="Camptocamp Dockerfile"
LABEL io.github.jalvcl.based_on_url="https://github.com/camptocamp/docker-odoo-project"

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# build and dev packages
ENV BUILD_PACKAGE \
    build-essential \
    gcc \
    python3.5-dev \
    libevent-dev \
    libfreetype6-dev \
    libxml2-dev \
    libxslt1-dev \
    libsasl2-dev \
    libldap2-dev \
    libssl-dev \
    libjpeg-dev \
    libpng-dev \
    zlib1g-dev \
    git

COPY ./install /install

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main" > /etc/apt/sources.list.d/pgdg.list' \
        && /install/package_odoo_11.0_12.0.sh \
        && /install/setup-pip.sh \
        && /install/postgres.sh \
        && /install/wkhtml_12_4.sh \
        && /install/dev_package.sh \
        && python3 -m pip install --force-reinstall pip setuptools

# grab gosu for easy step-down from root and dockerize to generate template and
# wait on postgres
#suspend instalation bug https://github.com/tianon/gosu/issues/35 

#RUN /install/gosu.sh && /install/dockerize.sh

# Install Odoo
ENV ODOO_VERSION 11.0
ENV ODOO_RELEASE 20181113
RUN set -x; \
        curl -o odoo.deb -SL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}.${ODOO_RELEASE}_all.deb \
        && echo '2413baffcde667b526673e846b24ca3f8e429853 odoo.deb' | sha1sum -c - \
        && dpkg --force-depends -i odoo.deb \
        && apt-get update \
        && apt-get -y install -f --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* odoo.deb

# Mount /var/lib/odoo to allow restoring filestore and /odoo/extra-addons for users addons
RUN mkdir -p /odoo /odoo/extra-addons /odoo/test-addons /var/log/odoo \
        && chown -R odoo /odoo/extra-addons \
        && chown -R odoo /odoo/test-addons \
        && chown -R odoo /var/log/odoo

COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/
RUN chown odoo /etc/odoo/odoo.conf

VOLUME ["/var/lib/odoo", "/odoo/extra-addons", "/odoo/test-addons", "/var/log/odoo"]

# Expose Odoo services
EXPOSE 8069 8071

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]