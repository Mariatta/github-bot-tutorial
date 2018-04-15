Using gidgethub on the command line
===================================

Let's do some simple exercises of using GitHub API to create an issue. We'll
be doing this locally using the command line, instead of actually creating the issue
in GitHub website.

Install gidgethub and aiohttp
-----------------------------

Install ``gidgethub`` and ``aiohttp`` if you have not already. Using a virtual environment
is recommended.

::

   pip install gidgethub
   pip install aiohttp

Create GitHub Personal Access Token
-----------------------------------

In order to use GitHub API, you'll need to create a personal access token
that will be used to authenticate yourself to GitHub.

1. Go to https://github.com/settings/tokens.

   Or, from GitHub, go to your `Profile Settings`_ > `Developer Settings`_ > `Personal access tokens`_.

2. Click Generate new token.

3. Under ``Token description``, enter a short description, to identify the purpose
   of this token. I recommend something like: ``pycon bot tutorial``.

4. Under select scopes, check the ``repo`` scope. You can read all about the available
   scopes `here <https://developer.github.com/apps/building-oauth-apps/scopes-for-oauth-apps/>`_.
   In this tutorial, we'll only be using the token to work with repositories,
   and nothing else. But this can be edited later. What the ``repo`` scope allows your
   bot to do is explained in
   `GitHub's scope documentation <https://developer.github.com/apps/building-oauth-apps/scopes-for-oauth-apps/#available-scopes>`__.

5. Press generate. You will see a really long string (40 characters). Copy that,
   and paste it locally in a text file for now.

   This is the only time you'll see this token in GitHub. If you lost it, you'll
   need to create another one.


Store the Personal Access Token as an environment variable
----------------------------------------------------------

In Unix / Mac OS::

   export GH_AUTH=your token

In Windows::

   set GH_AUTH=your token

Note that these will only set the token for the current process. If you want
this value stored permanently, you have to edit the bashrc file.


Let's get coding!
-----------------

Create a new Python file, for example: ``create_issue.py``, and open up your text
editor.


Copy the following into ``create_issue.py``::

    import asyncio

    async def main():
        print("Hello world.")

    loop = asyncio.get_event_loop()
    loop.run_until_complete(main())


Save and run it in the command line::

    python3 -m create_issue


You should see "Hello world." printed. That was "Hello world" with asyncio! 😎


Create an issue
---------------

Ok now we want to actually work with GitHub and ``gidgethub``.

Add the following imports::

   import os
   import aiohttp
   from gidgethub.aiohttp import GitHubAPI

And replace ``print("Hello world.")`` with::

   async with aiohttp.ClientSession() as session:
       gh = GitHubAPI(session, "mariatta", oauth_token=os.getenv("GH_AUTH"))


Instead of "mariatta" however, use your own GitHub username.

The full code now looks like the following::

   import asyncio
   import os
   import aiohttp
   from gidgethub.aiohttp import GitHubAPI

   async def main():
       async with aiohttp.ClientSession() as session:
           gh = GitHubAPI(session, "mariatta", oauth_token=os.getenv("GH_AUTH"))

   loop = asyncio.get_event_loop()
   loop.run_until_complete(main())

So instead of printing out hello world, we're now instantiating a GitHub API from
gidgethub, we're telling it who we are ("mariatta" in this example), and we're
giving it the GitHub personal access token, which were stored as the ``GH_AUTH``
environment variable.

Now, let's create an issue in my personal repo.

Take a look at GitHub's documentation for `creating a new issue`_.

It says, you can create the issue by making a ``POST`` request to the url
``/repos/:owner/:repo/issues`` and supply the parameters like ``title`` (required)
and ``body``.

With gidgethub, this looks like the following::

   await gh.post('/repos/mariatta/strange-relationship/issues',
                 data={
                     'title': 'We got a problem',
                     'body': 'Use more emoji!',
                 })

Go ahead and add the above code right after you instantiate GitHubAPI.

Your file should now look like the following::

    import asyncio
    import os
    import aiohttp
    from gidgethub.aiohttp import GitHubAPI

    async def main():
       async with aiohttp.ClientSession() as session:
           gh = GitHubAPI(session, "mariatta", oauth_token=os.getenv(GH_AUTH))
           await gh.post('/repos/mariatta/strange-relationship/issues',
                 data={
                     'title': 'We got a problem',
                     'body': 'Use more emoji!',
                 })

    loop = asyncio.get_event_loop()
    loop.run_until_complete(main())

Feel free to change the title and the body of the message.

Save and run that. There should be a new issue created in my repo. Check it out:
https://github.com/mariatta/strange-relationship/issues


Comment on issue
----------------

Let's try a different exercise, to get ourselves more familiar with GitHub APIs.

Take a look at GitHub's `create a comment`_ documentation.

Try this yourself, and leave a comment in the issue you just created.


Close the issue
---------------

Let's now close the issue that you've just created.

Take a look at the documentation to `edit an issue`_.

The method for deleting an issue is ``PATCH`` instead of ``POST``, which we've
seen in the previous two examples. In addition, to delete an issue, you're basically
editing an issue, and setting the ``state`` to ``closed``.

Use gidgethub to patch the issue::

   await gh.patch('/repos/mariatta/strange-relationship/issues/28',
                  data={'state', 'closed'},
                  )


Replace ``28`` with the issue number you created.


Bonus exercise
--------------

`Add reaction`_ to an issue.


.. _`Profile Settings`: https://github.com/settings/profile
.. _`Developer Settings`: https://github.com/settings/developers
.. _`Personal access tokens`: https://github.com/settings/tokens

.. _`creating a new issue`: https://developer.github.com/v3/issues/#create-an-issue
.. _`create a comment`: https://developer.github.com/v3/issues/comments/#create-a-comment
.. _`edit an issue`: https://developer.github.com/v3/issues/#edit-an-issue
.. _`Add reaction`: https://developer.github.com/v3/reactions/#create-reaction-for-an-issue
