FROM odoo:11
LABEL maintainer="jalv jlvzenzey@yahoo.com"
LABEL version="11_base"
LABEL repository="https://github.com/Jalvcl/03.01_Docker_Odoo_Base"
LABEL description="Clean Odoo 11 for further development, with minor modificatons"

USER root
# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/test-addons \
        && chown -R odoo /mnt/test-addons
VOLUME ["/var/lib/odoo", "/mnt/extra-addons", "/mnt/test-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

#from error message in odoo11 first run "odoo.addons.sms.wizard.send_sms: The `phonenumbers` Python module is not available. Phone number validation will be skipped. Try `pip3 install phonenumbers` to install it."
RUN pip3 install phonenumbers

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]