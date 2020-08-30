Resources
==========

1. `Django Development with Docker Compose and Machine (RealPython) <https://realpython.com/django-development-with-docker-compose-and-machine/>`_

2. `github example <https://github.com/pahaz/docker-compose-django-postgresql-redis-example>`_

3. `Deploying a full Django stack with Docker-Compose <https://www.capside.com/labs/deploying-full-django-stack-with-docker-compose/>`_

4. `pip3: command not found <https://stackoverflow.com/questions/48014769/pip3-command-not-found>`_

5. `docker-alpine issue (github) <https://github.com/alpinelinux/docker-alpine/issues/91>`_

6. `SO: Error: could not determine PostgreSQL version from 10.4 <https://askubuntu.com/questions/1059057/error-could-not-determine-postgresql-version-from-10-4>`_

7. `Django Tutorial by Docker <https://docs.docker.com/compose/django/>`_

8. `Persistent Postgres Database <https://www.digitalocean.com/community/questions/how-to-create-a-persistent-data-volume-for-postgres-database-container-within-a-docker-project>`_

Approach
==============

So following resources #1 and #2 were both not successful.  I think most of relates to
the fact that I was looking for an ultra specific and relatively complicated tutorial.
If you are looking for a tutorial on django + redis + docker + postgres + gunicorn, 
you likely already have 
at least one of those pieces.  Probably django, but unsurprisingly they tutorials
assume everything is from scratch.  They also do not generally take an incremental
approach that identifies issues along the way.  It is the "big bang" approach often
associated with  "Waterfall" style projects where multiple technologies will obviously
integrated perfectly together without issue right at the end.

So after much pain, I took a step back simply looked at django and docker.

I finally had some success with Resource #7.  So I plan to start from there and build
to where I want to be.

Deviations from Resource #7
============================

Additional Requirements
--------------------------------

I added a lot more content to ``requirements.txt`` because my existing django project
has a lot more depdencies than just django.

Existing Django Configuration
----------------------------------------

My django content is also in a directory that we will call ``django_project`` for this 
example.  So I needed to modify ``command`` for the web stage in ``docker-compose.yml``.

I also need the database to be setup with my migrations.  So let's go ahead and create
a bash script (``django_setup.sh``) for this stuff:

.. code-block:: bash

    cd django_project
    python manage.py migrate
    python manage.py runserver 0.0.0.0:8000

.. code-block:: yaml

    version: '3'
    
    services:
    db:
        image: postgres
        environment:
        - POSTGRES_DB=postgres
        - POSTGRES_USER=postgres
        - POSTGRES_PASSWORD=postgres
    web:
        build: .
        command: bash ./django_setup.sh
        volumes:
        - .:/code
        ports:
        - "8000:8000"
        depends_on:
        - db

Additions
=========

Environment Variable File
----------------------------

I did like how Resource #3 did the environment variable file.  I adopted that method
with modifications:

``env``:

.. code-block:: bash

    POSTGRES_DB=db_web
    POSTGRES_USER=user_web
    POSTGRES_PASSWORD=alksjdflkjdjjej
    DB_SERVICE=db
    DB_PORT=5432

``docker-compose.yml``:

.. code-block:: yaml

    version: '3'
    
    services:
        db:
            image: postgres
            env_file: env
        web:
            build: .
            command: bash ./django_start.sh
            volumes:
            - .:/code
            ports:
            - "8000:8000"
            depends_on:
            - db
            env_file: env

.. note:: See the use of ``env`` in both services.

I also liked how they implemented the django ``settings.py`` to allow
local development with sqlite file.  So I adopted that:

.. note:: The environment variable names here must also match the ``env`` file.

.. code-block:: python

    if 'POSTGRES_DB' in os.environ:
        # Running the Docker image
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.postgresql',
                'NAME': os.environ['POSTGRES_DB'],
                'USER': os.environ['POSTGRES_USER'],
                'PASSWORD': os.environ['POSTGRES_PASSWORD'],
                'HOST': os.environ['DB_SERVICE'],
                'PORT': os.environ['DB_PORT']
            }
        }
    else:
        # Building the Docker image
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.sqlite3',
                'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
            }
        }

Redis and Celery
----------------------------

My django application utilizes celery via redis for long running tasks.
I add the following to ``docker-compose.yml``:

.. code-block:: yaml

  redis:
    restart: always
    image: redis:latest
    expose:
      - "6379"

  celery:
    build: .
    command: bash ./start_celery.sh
    volumes:
      - .:/code
    depends_on:
      - redis
    env_file: env

I also added a ``depends_on`` to service ``web`` for ``celery``.

``start_celery.sh`` simply changes into the appropriate directory
and starts the celery worker, i.e. ``celery worker -A ...``.

With this setup, you also need to properly configure your django
``settings.py`` something like this:

.. code-block:: python

    # celery
    CELERY_BROKER_URL = 'redis://redis:6379'
    CELERY_RESULT_BACKEND = 'redis://redis:6379'