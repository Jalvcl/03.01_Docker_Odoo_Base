FROM jalvcl/odoo_base:11p.01
LABEL io.github.jalvcl.maintainer="jalv jlvzenzey@yahoo.com"
LABEL io.github.jalvcl.version="11p.01.01"
LABEL io.github.jalvcl.repository="https://github.com/Jalvcl/03.01_Docker_Odoo_Base"
LABEL io.github.jalvcl.description="Odoo 11 with OCA modules"
LABEL io.github.jalvcl.based_on="Camptocamp Dockerfile"
LABEL io.github.jalvcl.based_on_url="https://github.com/camptocamp/docker-odoo-project"

COPY ./base_and_extra_requirements.txt /odoo

# Divide the Install to detect error in dockerbuild
RUN set -x; \
        python3 -m pip install --force-reinstall pip setuptools \
        && pip3 install -r /odoo/base_and_extra_requirements.txt --ignore-installed \
        && /install/purge_dev_package_and_cache.sh

# Mount /var/lib/odoo to allow restoring filestore and /odoo/extra-addons for users addons
RUN mkdir -p /odoo/oca-addons \
        && chown -R odoo /odoo/oca-addons \

COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/
RUN chown odoo /etc/odoo/odoo.conf

VOLUME ["/var/lib/odoo", "/odoo/extra-addons", "/odoo/test-addons", "/var/log/odoo", "/odoo/oca-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]