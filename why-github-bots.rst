GitHub Bots: What and Why
=========================

Welcome to the Build-a-GitHub Bot workshop!

What are GitHub bots?
---------------------

Applications that runs automation on GitHub, using GitHub WebHooks and APIs.

What can bots do?
-----------------

Many things: it can automatically respond to users, apply labels, close issues,
create new issues, and even merge pull requests. Use the extensive GitHub APIs
and automate your workflow!

Why use bots?
-------------

By automating your workflow, you can focus on real collaboration, instead of
getting stuck doing boring housekeeping things.

Example GitHub bots
-------------------

the-knight-who-says-ni
''''''''''''''''''''''

Source code: https://github.com/python/the-knights-who-say-ni

Waits for incoming CPython's pull requests. Each time a pull request is opened,
it does the following:

  - find out the author's info

  - find out if the author has signed the CLA

  - if the author has not signed the CLA, notify the author

  - if the author has signed the CLA, apply the CLA signed Label

bedevere-bot
''''''''''''

Source code: https://github.com/python/bedevere

Performs status checks, identify issues and stages of the pull request.
Some tasks that bedevere-bot does:

  - identify the stage of the pull request, one of:  awaiting review, awaiting merge,
    awaiting change request, awaiting core dev review.

  - apply labels to pull requests

  - checks if the PR contains reference to an issue

  - automatically provide link to the issue in the bug tracker


miss-islington
''''''''''''''

Source code: https://github.com/python/miss-islington

Automatically create backport pull requests and reminds core devs that status checks
are completed.

In addition, miss-islington can also automatically merge the pull request, and
delete merged branch.
