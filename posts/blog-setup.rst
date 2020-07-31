.. title: Blog Setup
.. slug: blog-setup
.. date: 2020-07-31 02:32:30 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Purpose
==============

Documenting the process of making this blog and website.

Resources
=============

Resources I found helpful.

1. `Personal Website with Jupyter Support using Nikola and GitHub Page <https://jiaweizhuang.github.io/blog/nikola-guide/>`_

2. `Nikola Blog <https://getnikola.com/blog/>`_

Instructions
=============

I followed the instructions in resource #1 exactly with the following exceptions:

1. I followed the style of ``conf.py`` when adding notebook support:

.. code-block::

    POSTS = (
        ("posts/*.rst", "posts", "post.tmpl"),
        ("posts/*.md", "posts", "post.tmpl"),
        ("posts/*.txt", "posts", "post.tmpl"),
        ("posts/*.html", "posts", "post.tmpl"),
        ("posts/*.ipynb", "posts", "post.tmpl"),
    )
    PAGES = (
        ("pages/*.rst", "pages", "page.tmpl"),
        ("pages/*.md", "pages", "page.tmpl"),
        ("pages/*.txt", "pages", "page.tmpl"),
        ("pages/*.html", "pages", "page.tmpl"),
        ("pages/*.ipynb", "pages", "page.tmpl"),
    )
