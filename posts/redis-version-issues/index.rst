.. title: Redis Version Issues
.. slug: redis-version-issues
.. date: 2020-08-09 03:14:37 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Resources
===========

1. ` Redis Celery Django Setup <https://stackabuse.com/asynchronous-tasks-in-django-with-redis-and-celery/>`_

Background
==========

I have a Django side project.  I am using celery + redis for some long running
tasks.

Notes
======

I have run into this issue in the past, and it is super confusing to debug.
A random redis error from the celery process is thrown with almost no 
helpful information:

.. code-block::

    redis.exceptions.ResponseError: wrong number of arguments for 'set' command

    redis.exceptions.ResponseError: wrong number of arguments for 'ping' command

In my case it is due to one (very old) version of redis being installed on my
computer and obviously in my path.  You have probably followed the instructions
of a resource like #1 above.  It instructs to download the tarball, extract,
then ``make install``.  After this you should be able to magically
``redis-server``.  In my case, ``redis-server`` does indeed run redis, but
it runs a very old version of redis (2.6.9 in my case).

.. code-block::

    [74237] 08 Aug 23:08:08.159 * Max number of open files set to 10032
                    _._                                                  
            _.-``__ ''-._                                             
        _.-``    `.  `_.  ''-._           Redis 2.6.9 (00000000/0) 64 bit
    .-`` .-```.  ```\/    _.,_ ''-._                                   
    (    '      ,       .-`  | `,    )     Running in stand alone mode
    |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
    |    `-._   `._    /     _.-'    |     PID: 74237
    `-._    `-._  `-./  _.-'    _.-'                                   
    |`-._`-._    `-.__.-'    _.-'_.-'|                                  
    |    `-._`-._        _.-'_.-'    |           http://redis.io        
    `-._    `-._`-.__.-'_.-'    _.-'                                   
    |`-._`-._    `-.__.-'    _.-'_.-'|                                  
    |    `-._`-._        _.-'_.-'    |                                  
    `-._    `-._`-.__.-'_.-'    _.-'                                   
        `-._    `-.__.-'    _.-'                                       
            `-._        _.-'                                           
                `-.__.-'                                               

    [74237] 08 Aug 23:08:08.163 # Server started, Redis version 2.6.9

.. note:: Notice the version number(s) in the terminal.

Redis at the time of this writing is at version 6.0.6.  Even running
``redis-server`` in the src folder where it was built is not successful.

Success for me involved putting the terminal in the main redis directory
and executing:

.. code-block::

    src/redis-server

This produces the expected redis version execution:

.. code-block::

    4319:M 08 Aug 2020 23:11:21.226 * Increased maximum number of open files to 10032 (it was originally set to 256).
                    _._                                                  
            _.-``__ ''-._                                             
        _.-``    `.  `_.  ''-._           Redis 6.0.6 (00000000/0) 64 bit
    .-`` .-```.  ```\/    _.,_ ''-._                                   
    (    '      ,       .-`  | `,    )     Running in standalone mode
    |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
    |    `-._   `._    /     _.-'    |     PID: 74319
    `-._    `-._  `-./  _.-'    _.-'                                   
    |`-._`-._    `-.__.-'    _.-'_.-'|                                  
    |    `-._`-._        _.-'_.-'    |           http://redis.io        
    `-._    `-._`-.__.-'_.-'    _.-'                                   
    |`-._`-._    `-.__.-'    _.-'_.-'|                                  
    |    `-._`-._        _.-'_.-'    |                                  
    `-._    `-._`-.__.-'_.-'    _.-'                                   
        `-._    `-.__.-'    _.-'                                       
            `-._        _.-'                                           
                `-.__.-'                                               

    74319:M 08 Aug 2020 23:11:21.231 # Server initialized
    74319:M 08 Aug 2020 23:11:21.233 * Loading RDB produced by version 6.0.6
