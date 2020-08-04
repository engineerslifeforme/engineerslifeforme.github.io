.. title: Edit Git Author History
.. slug: edit-git-author-history
.. date: 2020-08-04 02:58:11 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Purpose
=======

As I mentioned in a previous post, I am attempting to keep the git
history of the blog prestine.  Well, I switched to another computer
and immediately messed it up.  

In the good news category, having
all my notes on github is really handy.  The rendering of the 
draft rst posts was especially handy.  I didn't even need to clone
the repo to access some links I had gathered.

So the purpose of this post is to capture the process of fixing my
mistake.

Resources
===========

1. `Changing author info (Github docs) <https://docs.github.com/en/github/using-git/changing-author-info>`_

Instructions
=============

.. note:: This is probably useless or very painful if you have multiple
collaboraters.  A ``git fetch`` will not fix other repos to match.

1. Create a place to clone a fresh copy of the repo

.. code-block::

    mkdir temp_blog
    cd temp_blog
    git clone --bare https://BLOG_URL
    cd BLOG_REPO

2. Go into your commits and find the offending commit(s).  At the terminal use ``git log``.
You should see something like:

.. code-block::

    commit fb786d1a0782810299d0a58a8155d106c58e94e8 (HEAD -> src)
    Author: BAD_NAME <OLD_EMAIL>
    Date:   Mon Aug 3 16:31:09 2020 -0400

        More drafting on balance-board

    commit 4e8cce32080b3832af2f5815cb9654abb3cbe040
    Author: CORRECT_NAME <CORRECT_EMAIL>
    Date:   Sun Aug 2 22:42:28 2020 -0400

        writing

3. Create the script they provide filling in ``OLD_EMAIL``, ``CORRECT_NAME``,
and ``CORRECT_EMAIL``.  I named mine ``fix.sh``.

.. code-block::

    #!/bin/sh

    git filter-branch --env-filter '

    OLD_EMAIL="your-old-email@example.com"
    CORRECT_NAME="Your Correct Name"
    CORRECT_EMAIL="your-correct-email@example.com"

    if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
    then
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
    fi
    if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
    then
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
    fi
    ' --tag-name-filter cat -- --branches --tags

4. Run the script

.. code-block::

    bash fix.sh

I got an output like this:

.. code-block::

    Rewrite 6c9f6249c2c194676fe3aa3fdb5a0a83eab3d635 (1/7) (0 seconds passed, remainRewrite a18de98856d9c03740df59ddde8059f0fcb42509 (2/7) (0 seconds passed, remainRewrite 37b52d143b39f05c62851e044259ab8d1fe97725 (3/7) (0 seconds passed, remainRewrite c4f57f07893ba7c22b594f08d76794ab0b22b1a5 (4/7) (0 seconds passed, remainRewrite 63754887571d3eb125b460e3c4144f31b86c9bb9 (5/7) (0 seconds passed, remainRewrite 4e8cce32080b3832af2f5815cb9654abb3cbe040 (6/7) (0 seconds passed, remainRewrite fb786d1a0782810299d0a58a8155d106c58e94e8 (7/7) (0 seconds passed, remaining 0 predicted)    
    WARNING: Ref 'refs/heads/master' is unchanged
    Ref 'refs/heads/src' was rewritten

5. Double check that the commit data you wanted changed is changed:
``git log``.  Mine was all good.

6. Push the new history to the server:

.. code-block::

    git push --force --tags origin 'refs/heads/*'

7. You should see the changes in the web view of the repo as well now.

8. You should delete ``temp_blog`` now.

9. You should delete and reclone all versions of the repository.

9. Follow the instructions in my previous post to avoid this problem in the future:
`Blog Repo Setup <link://slug/blog-repo-setup>`_
