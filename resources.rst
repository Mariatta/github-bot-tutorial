Resources
=========

Tools and documentations that we'll use throughout this tutorial.

venv
----

See also: `Python venv tutorial`_ documentation.

It is recommended that you install the Python packages inside a virtual environment.
For this tutorial, we'll use ``venv``.

Create a new virtual environment using venv::

   python3 -m venv tutorial-env

Activate the virtual environment. On Unix, Mac OS::

   source tutorial-env/bin/activate

On Windows::

   tutorial-env\Scripts\activate.bat

GitHub API v3 documentation
---------------------------

- `Issues API`_

- `Pull requests API`_

- `Reactions API`_

- `Event Types & Payloads`_

gidgethub
---------

- Installation: ``pip install gidgethub``.

- `gidgethub documentation`_

- `gidgethub`_ source code

- Owner: `Brett Cannon <https://brettsky.ca>`_

aiohttp
-------

- Installation: ``pip install aiohttp``.

- `aiohttp documentation`_

- `aiohttp`_ source code

- Owner: `Andrew Svetlov <http://asvetlov.blogspot.ca/>`_


f-strings
---------

We will use some f-strings during this tutorial.

My `talk <https://speakerdeck.com/mariatta/pep-498-the-monologue>`_ about f-strings.

Example::

   first_name = "bart"
   last_name = "simpson"
   
   # old style %-formatting
   print("Hello %s %s" % (first_name, last_name))
   
   # str.format
   print("Hello {first_name} {last_name}".format(first_name=first_name, last_name=last_name))
   
   # f-string
   print(f"Hello {first_name} {last_name}")
  
asyncio
-------

Both `gidgethub` and `aiohttp` are async libraries. Read up the `quick intro <https://www.blog.pythonlibrary.org/2016/07/26/python-3-an-intro-to-asyncio/>`_
to asyncio.

Heroku
------

`Python on Heroku`_ documentation.


.. _`Python venv tutorial`: https://docs.python.org/3/tutorial/venv.html

.. _`Issues API`: https://developer.github.com/v3/issues/
.. _`Pull requests API`: https://developer.github.com/v3/pulls/
.. _`Reactions API`: https://developer.github.com/v3/reactions/
.. _`Event Types & Payloads`: https://developer.github.com/v3/activity/events/types/


.. _`gidgethub documentation`: https://gidgethub.readthedocs.io
.. _`aiohttp documentation`: https://aiohttp.readthedocs.io

.. _`Python on Heroku`: https://devcenter.heroku.com/categories/python


   
