Resources
==========

1. `Django Development with Docker Compose and Machine (RealPython) <https://realpython.com/django-development-with-docker-compose-and-machine/>`_

2. `github example <https://github.com/pahaz/docker-compose-django-postgresql-redis-example>`_

3. `Deploying a full Django stack with Docker-Compose <https://www.capside.com/labs/deploying-full-django-stack-with-docker-compose/>`_

Modifications
==============

I already have python installed for local development, and my django site is
already setup.

Modified to execute in my project directory.

.. code-block::

    docker run -ti --rm -v web/:/data/web alpine:latest sh
    / # cd data/web/
    /data/web # apk add --update python3 python3-dev postgresql-client postgresql-dev build-base gettext vim
    /data/web # pip3 install --upgrade pip
    /data/web # pip3 install -r requirements.txt