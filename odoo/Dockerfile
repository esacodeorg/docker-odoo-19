FROM odoo:19
LABEL MAINTAINER="support@esacode.org"

USER root

RUN apt-get update      && \
    apt-get upgrade -y  && \
    apt-get install -y build-essential sox python3 python3-dev python3-pip

# COPY addons/requirements.txt /requirements.txt
# RUN pip install -r /requirements.txt

# USER odoo