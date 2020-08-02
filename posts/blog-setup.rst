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

`EngineersLifeForMe <https://engineerslifeforme.github.io>`_

Resources
=============

Resources I found helpful.

1. `Personal Website with Jupyter Support using Nikola and GitHub Page <https://jiaweizhuang.github.io/blog/nikola-guide/>`_

2. `Nikola Blog <https://getnikola.com/blog/>`_

3. `Nikola ReadTheDocs <https://nikola.readthedocs.io/en/latest/manual/#getting-started>`_

4. `How to blog with Jupyter (IPython) Notebook and Nikola <http://www.jaakkoluttinen.fi/blog/how-to-blog-with-jupyter-ipython-notebook-and-nikola/>`_

Instructions
=============

I followed the instructions in resource #1 exactly with the following exceptions:

1. You may want to consider following my instructions on setting the git config
for the repo here: 

2. I followed the style of ``conf.py`` when adding notebook support:

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

3. I created an ``About`` page similar to the examples Bio page.  Adding this page to
the Navigation links did not work.  I had to use:

.. code-block::

    NAVIGATION_LINKS = {
        DEFAULT_LANG: (
            ("/archive.html", "Archives"),
            ("/categories/index.html", "Tags"),
            ("/rss.xml", "RSS feed"),
            ("/index.html", "Home"),
            ("/pages/about/index.html", "About"),
        ),
    }