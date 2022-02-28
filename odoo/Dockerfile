#FROM camptocamp/odoo-project:14.0-latest-batteries as base
FROM ghcr.io/camptocamp/odoo-project:14.0-latest-batteries as base
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    git \
    gcc \
    build-essential \
    python3-dev \
    python3-pykcs11 \
    pkg-config \
    libxmlsec1-dev \
    libxml2-dev \
    libxmlsec1-dev \    
    libevent-dev \
    libfreetype6-dev \
    libxslt1-dev \
    libsasl2-dev \
    libldap2-dev \
    libssl-dev \
    libjpeg-dev \
    libpng-dev \
    locales \
    swig \
    zlib1g-dev \
    postgresql-client-common \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN sed -i -e 's/# pt_BR.UTF-8 UTF-8/pt_BR.UTF-8 UTF-8/' /etc/locale.gen \
    && dpkg-reconfigure --frontend=noninteractive locales \
    && update-locale LANG=pt_BR.UTF-8
    
COPY ./requirements.txt /odoo/requirements.txt
RUN pip install -r /odoo/requirements.txt

COPY ./VERSION /odoo/VERSION
COPY ./setup.py /odoo/setup.py
COPY ./songs /odoo/songs
COPY ./migration.yml /odoo/migration.yml
COPY ./templates /templates
COPY ./links /odoo/links

# entrypoint scripts that will run before the container starts
COPY entrypoint/002_click_odoo_update /start-entrypoint.d/002_click_odoo_update
RUN chmod 655 /start-entrypoint.d/*

FROM base as dev

# Put here some stuff only for dev
COPY ./src/setup.py /odoo/src/setup.py
COPY ./src/setup.cfg /odoo/src/setup.cfg
COPY ./src/setup /odoo/src/setup
COPY ./src/odoo/release.py /odoo/src/odoo/release.py
COPY ./src/requirements.txt /odoo/requirements.txt
RUN pip install -e /odoo/src
RUN pip install -e /odoo

FROM base as prod

# Used by prod and ci

COPY ./local-src /odoo/local-src
COPY ./src /odoo/src
RUN pip install -e /odoo/src
COPY ./external-src /odoo/external-src
RUN pip install -e /odoo

