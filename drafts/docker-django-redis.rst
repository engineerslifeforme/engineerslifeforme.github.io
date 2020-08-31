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

9. `Dockerizing Django with Postgres, Gunicorn, and Nginx <https://testdriven.io/blog/dockerizing-django-with-postgres-gunicorn-and-nginx/>`_

10. `docker-compose.yml documentation <https://docs.docker.com/compose/compose-file/#environment>`_

11. `django manage documentation <https://docs.djangoproject.com/en/3.1/ref/django-admin/#flush>`_

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

Resource #9 had a slick implementation of the django ``settings.py`` to allow
local development with sqlite file.  So I adopted that:

.. note:: The environment variable names here must also match the ``env`` file.

.. code-block:: python

    DATABASES = {
        "default": {
            "ENGINE": os.environ.get("SQL_ENGINE", "django.db.backends.sqlite3"),
            "NAME": os.environ.get("POSTGRES_DB", os.path.join(BASE_DIR, "db.sqlite3")),
            "USER": os.environ.get("POSTGRES_USER", "user"),
            "PASSWORD": os.environ.get("POSTGRES_PASSWORD", "password"),
            "HOST": os.environ.get("SQL_HOST", "localhost"),
            "PORT": os.environ.get("SQL_PORT", "5432"),
        }
    }

Resource #9 moves a few additional items to environment variables

Additions to ``.env``:

.. code-block:: bash

    SECRET_KEY=crazy_long_string_of_nonsense
    DEBUG=1
    DJANGO_ALLOWED_HOSTS=localhost 127.0.0.1 [::1]

And associated changes to ``settings.py``:

.. code-block:: python

    # SECURITY WARNING: keep the secret key used in production secret!
    SECRET_KEY = os.environ.get('SECRET_KEY')

    # SECURITY WARNING: don't run with debug turned on in production!
    DEBUG = int(os.environ.get("DEBUG", default=0))

    # 'DJANGO_ALLOWED_HOSTS' should be a single string of hosts with a space between each.
    # For example: 'DJANGO_ALLOWED_HOSTS=localhost 127.0.0.1 [::1]'
    ALLOWED_HOSTS = os.environ.get("DJANGO_ALLOWED_HOSTS").split(" ")

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

Postgres Startup issues
----------------------------

Resource #9 indicates that there may sometimes be issues with postgres
startup.  I think I experience this once.  I could not use the provided
logic on the first try.  I don't think ``nc`` was availble.