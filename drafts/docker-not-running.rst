Resources
===========

1. `Uninstall Docker (StackOverflow) <https://stackoverflow.com/questions/44346109/how-to-easily-install-and-uninstall-docker-on-macos>`_

2. `Docker Desktop on Mac vs. Docker Toolbox <https://docs.docker.com/docker-for-mac/docker-toolbox/>`_

3. `How to uninstall Toolbox <https://docs.docker.com/toolbox/toolbox_install_mac/#how-to-uninstall-toolbox>`_

4. `Install Docker Desktop on Mac <https://docs.docker.com/docker-for-mac/install/>`_

Symptoms
=========

I don't use docker all that often, so the errors are pretty confusing when I do.

.. code-block::

    docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?.
    See 'docker run --help'.

Actions Taken
==============

Given that I was already unhappy with my version of docker, I decided to attempt to remove
it and install a brand new version.  Followed instructions in Resource #1.  Specifically,
the removal of files in the brew answer.

Resource #2 was pretty enlightening on why ``docker-machine`` always felt so awkward.

I suspect all I really needed to do to fix my orinal symptoms was this:

.. code-block::

    eval $(docker-machine env default)

FWIW, the above did not help me on my mac mini where I had not uninstalled Docker Toolbox.
I had no machines when ``docker-machine ls``, so I decided to go ahead and follow the 
instructions in resource 3.  Then I will install Docker Desktop for Mac.

After the fresh install all seems to be well with ``docker`` CLI commands.