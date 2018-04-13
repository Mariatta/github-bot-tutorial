.. github-bot-tutorial documentation master file, created by
   sphinx-quickstart on Sat Apr  7 10:39:56 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Build-a-GitHub-Bot Workshop
===========================

GitHub provides a great platform for collaborating. You can take it to the next
level by creating custom GitHub bots. By delegating some of the chores to a bot,
you get to spend more time developing your project and collaborating with others.

Learn how to automate your workflow by building a personal GitHub assistant for
your own project. We'll use libraries called `gidgethub <https://gidgethub.readthedocs.io>`_
and `aiohttp <https://aiohttp.readthedocs.io>`_ to write a
GitHub bot that does the following:

  - :ref:`Greet the person who created an issue in your project <greet_author>`.

  - :ref:`Say thanks when a pull request has been closed <say_thanks>`.

  - :ref:`Apply a label to issues or pull requests <label_prs>`.

  - :ref:`Gives a thumbs up reaction to comments you made <react_to_comments>`. (becoming your own personal cheer squad).

The best part is, you get to do all of the above using Python 3.6! F-strings included!

This tutorial is for `PyCon US 2018 <https://us.pycon.org/2018/schedule/presentation/41/>`_
in Cleveland, Ohio. It will be recorded, and uploaded here once available.

About me
========

My name is Mariatta. I live in Vancouver, Canada where I work as a Software Engineer
for Zapier. In my free time, I help organize Vancouver PyLadies, PyCascades conference,
and contribute to open source.

I'm a Python Core Developer. I help maintain Core Python's GitHub bots:
`bedevere <https://github.com/python/bedevere>`_ and
`miss-islington <https://github.com/miss-islington>`_.

We'll be using the same tools and technologies used by Core Python's
team to build their bots.

If you have any feedback or questions about this tutorial, please file an issue.

- E-mail: mariatta@python.org
- Twitter: `@mariatta <https://twitter.com/mariatta>`_
- `Mariatta on GitHub <https://github.com/mariatta>`_
- `Mariatta on Patreon <https://www.patreon.com/Mariatta>`_

Know of other places, conferences, or events where I can give this tutorial?
Let me know!

Code of Conduct
===============

Be open, considerate, and respectful.

License
=======

`CC-BY-SA 4.0 <https://creativecommons.org/licenses/by-sa/4.0/>`_.

Agenda
======


.. toctree::
   :titlesonly:


   preparation
   why-github-bots
   resources
   gidgethub-cmd-line
   gidgethub-for-webhooks
   whats-next
   hall-of-fame
   git-basics
