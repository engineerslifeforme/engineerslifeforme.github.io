Resources
==========

1. `Django Development with Docker Compose and Machine (RealPython) <https://realpython.com/django-development-with-docker-compose-and-machine/>`_

2. `github example <https://github.com/pahaz/docker-compose-django-postgresql-redis-example>`_

3. `Deploying a full Django stack with Docker-Compose <https://www.capside.com/labs/deploying-full-django-stack-with-docker-compose/>`_

4. `pip3: command not found <https://stackoverflow.com/questions/48014769/pip3-command-not-found>`_

5. `docker-alpine issue (github) <https://github.com/alpinelinux/docker-alpine/issues/91>`_

6. `SO: Error: could not determine PostgreSQL version from 10.4 <https://askubuntu.com/questions/1059057/error-could-not-determine-postgresql-version-from-10-4>`_

7. `Django Tutorial by Docker <https://docs.docker.com/compose/django/>`_

Modifications
==============

I already have python installed for local development, and my django site is
already setup.

Changed psycopg2 dependency to have no version in ``requirements.txt``.

.. code-block::

    docker run -ti --rm -v /Users/nicholaspayne/projects/sight_word_explorer/kidlitai_deploy/web/:/data/web alpine:latest sh
    apk add --update python3 python3-dev postgresql-client postgresql-dev build-base gettext vim
    apk add cmd:pip3
    pip3 install --upgrade pip
    pip3 install -r requirements.txt

Modified to execute in my project directory.

.. code-block::

    docker run -ti --rm -v [ABSOLUTE PATH to web]:/data/web alpine:latest sh
    / # cd data/web/
    /data/web # apk add --update python3 python3-dev postgresql-client postgresql-dev build-base gettext vim
    /data/web # pip3 install --upgrade pip
    /data/web # pip3 install -r requirements.txt

.. note:: Fill in your absolute path to your web folder.

The above kind of looked like (at least to me) one big blob of commands
I could simply copy and paste into the terminal.  Nope.  Run the docker
command which effectively puts you into a shell in the container, then
run each of the commands individually.

Trouble with the first pip3 command:

.. code-block::

    sh: pip3: not found

I checked and ``python3`` worked fine, so must just not have pip.  Googling, Resource
#4 was not helpful.  #5 solved it, though, with:

.. code-block::

    apk add cmd:pip3

Installing ``requirements.txt`` also fails as follows:

.. code-block::

    Error: could not determine PostgreSQL version from '12.3'
    ----------------------------------------
    ERROR: Command errored out with exit status 1: python setup.py egg_info Check the logs for full command output.

Fixed by:

.. code-block::

     pip3 install --upgrade setuptools
     pip install -u psycopg2

``pip install pyscopg2`` did not work.  ``-U ==2.6'' did not work.