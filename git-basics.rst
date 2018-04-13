Git Cheat Sheet
===============

There are other more complete git tutorial out there, but the following should
help you during this workshop.

Clone a repo
------------

::

   git clone <url>


Create a new branch, and switch to it
-------------------------------------

::

   git checkout -b <branchname>


Switching to an existing branch
-------------------------------

::

   git checkout <branchname>

Adding files to be committed
----------------------------

::

   git add <filename>

Commit your changes
-------------------

::

   git commit -m "<commit message>"

Pushing changes to remote
-------------------------

::

   git push <remote> <branchname>


Rebase with a branch on remote
------------------------------

::

   git rebase <remote>/<branchname>


