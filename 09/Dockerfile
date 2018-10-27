FROM debian:jessie

# datos para la descarga de odoo
ENV ODOO_VERSION 9.0
ENV ODOO_RELEASE 20181027
ENV ODDO_SHA1SUM aeb2a1dbcdd8688d6063285c12daa95623aa04a3

#metadata
LABEL maintainer="jalv jlvzenzey@yahoo.com"
LABEL repository="https://github.com/Jalvcl/03.01_Docker_Odoo_Base"
LABEL description="Clean Odoo ${ODOO_VERSION}c (now deprecated) for further development, with minor modificatons (based on Dokerfile for odoo 12)"
LABEL version="9.0c Release 20181027"

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            node-less \
            #node-clean is not include in the original dockerfile of odoo 9
            node-clean-css \
            python-gevent \
            python-pip \
            #python-pyinotify is not include in the original dockerfile of odoo 9
            python-pyinotify \
            python-renderpm \
            python-support \
            python-watchdog\
        && curl -o wkhtmltox.deb -SL http://nightly.odoo.com/extra/wkhtmltox-0.12.1.2_linux-jessie-amd64.deb \
        && echo '40e8b906de658a2221b15e4e8cd82565a47d7ee8 wkhtmltox.deb' | sha1sum -c - \
        && dpkg --force-depends -i wkhtmltox.deb \
        && apt-get -y install -f --no-install-recommends \
        && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false npm \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb \
        && pip install psycogreen==1.0

# Install Odoo
RUN set -x; \
        curl -o odoo.deb -SL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}c.${ODOO_RELEASE}_all.deb \
        && echo "${ODDO_SHA1SUM} odoo.deb" | sha1sum -c - \
        && dpkg --force-depends -i odoo.deb \
        && apt-get update \
        && apt-get -y install -f --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* odoo.deb

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./openerp-server.conf /etc/odoo/
RUN chown odoo /etc/odoo/openerp-server.conf

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/extra-addons \
        && chown -R odoo /mnt/extra-addons
RUN mkdir -p /mnt/test-addons \
        && chown -R odoo /mnt/test-addons
VOLUME ["/var/lib/odoo", "/mnt/extra-addons", "/mnt/test-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV OPENERP_SERVER /etc/odoo/openerp-server.conf

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["openerp-server"]
