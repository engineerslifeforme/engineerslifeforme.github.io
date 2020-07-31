.. title: Blog Repo Setup
.. slug: blog-repo-setup
.. date: 2020-07-31 01:54:13 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Intro
========

I have likely too many git repository options that I have created over time.
Some private repos on bitbucket.  Repos for my LLC.  Public github repos.
And then I made the github account and repo for this blog.

Most of the time I am not terribly worried about how my git user info is shown
in the commit history, but given the intended public nature of this blog,
I did want to be intentional about my commit content.

Specifically, I want to assure that the commits on the blog repo consistently
show the email and user I have setup for this blog/repo.

Resources
===========

These are the resources I found helpful.

1. `How to set GitHub user.name and user.email per Repository? Different .config file for different Repository <https://crunchify.com/how-to-set-github-user-name-and-user-email-per-repository-different-config-for-different-repository/>`_

2. `Getting Started - First-Time Git Setup <https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup>`_

Instructions
==============

I'm assuming the repo has not even been initialized.

1. Initialize the repo.  My repo is in a folder called ``blog``

.. code-block::

    cd blog
    git init

2. Run the following git configuration commands (filling in your own details)

.. code-block::

    git config user.name "Creative Rigor, LLC"
    git config user.email creativerigor [at] gmail.com

Format your email address properly.

Definitely do not use the ``--global`` flag which will change the default
user settings for all repos.