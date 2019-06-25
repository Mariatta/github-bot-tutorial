Using gidgethub to respond to webhooks
======================================

In the previous example, we've been interacting with GitHub by doing actions:
making requests to GitHub. And we've been doing that locally on our own machine.

In this section we'll use what we know so far and start building an actual bot.
We'll create a webserver that responds to GitHub webhook events.

Webhook events
--------------

When an event is triggered in GitHub, GitHub can notify you about the event by
sending you a POST request along with the payload.

Some example ``events`` are:

- issues: any time an issue is assigned, unassigned, labeled, unlabeled, opened,
  edited, closed, reopened, etc.

- pull_request: any time a pull request is opened, edited, closed, reopened,
  review requested, etc.

- status: any time there's status update.

The complete list of events is listed `here <https://developer.github.com/webhooks/#events>`_.

Since GitHub needs to send you POST requests for the webhook, it can't send them
to your personal laptop. So we need to create a webservice that's open on the internet.

To the cloud!

Create two new repositories on GitHub
-------------------------------------

You'll need two repositories. The first one is to hold the codebase for the bot,
essentially the webservice. This is where you will actually push the codes.
Going forward I will call this the ``webservice``.

The other repo, will mostly be empty for now. It will be the repo where the
webhook will be installed, and your bot will interact with this repo.
In real world, this will be the project that you're managing. I will be using
my personal repo, ``strange-relationship``.

You can create the repositories on GitHub, and then clone it to your local
machine.

Create a webservice
-------------------

Let's get ready to write your own GitHub bot. To start, use your favorite text
editor or IDE. Go to the directory where your webservice is at.

Inside that directory, create a `requirements.txt` file. Add both `gidgethub` and
`aiohttp` to it.

requirements.txt::

   aiohttp
   gidgethub

In the same directory, create another directory with the same name as your
webservice/bot's name. Inside this new directory, create ``__main__.py``.

Your webservice should now look like the following::

   /webservice
   /webservice/requirements.txt
   /webservice/webservice/__main__.py


We'll start by creating a simple aiohttp webserver in ``__main__.py``.

Edit ``__main__.py`` as follows::

    import os

    from aiohttp import web

    async def main(request):
        return web.Response(status=200, text="Hello world!")


    if __name__ == "__main__":
        app = web.Application()
        app.router.add_get("/", main)
        port = os.environ.get("PORT")
        if port is not None:
            port = int(port)

        web.run_app(app, port=port)

Save the file. Your webserver is now ready. From the command line and at the root
of your project, enter the following::

   python3 -m webservice

You should now see the following output::

   ======== Running on http://127.0.0.1:8080 ========
   (Press CTRL+C to quit)

Open your browser and point it to http://127.0.0.1:8080.  Alternatively,
you can open another terminal and type::

   curl -X GET localhost:8080

Whichever method you choose, you should see the output: "Hello World".

Deploy to Heroku
----------------

Before we go further, let's first get that webservice deployed to Heroku.

At the root of your project, create a new file called ``Procfile``, (without any
extension). This file tells Heroku how it should run your app.

Inside ``Procfile``::

   web: python3 -m webservice

This will tell Heroku to run a web dyno using the command ``python3 -m webservice``.

Your file structure should now look like the following::

   /webservice
   /webservice/requirements.txt
   /webservice/Procfile
   /webservice/webservice/__main__.py


Commit everything and push to GitHub.

Login to your account on Heroku. You should land at https://dashboard.heroku.com/apps.

Click "New" > "Create a new app". Type in the app name, choose the United States region,
and click "Create app" button.
If you leave it empty, Heroku will assign a name for you.

Once your web app has been created, go to the Deploy tab. Under "Deployment method",
choose GitHub. Connect your GitHub account if you haven't done that.

Under "Search for a repository to connect to", enter your project name, e.g "webservice".
Press "Search". Once it found the right repo, press "Connect".

Scroll down. Under Deploy a GitHub branch, choose "master", and click "Deploy Branch".

Watch the build log, and wait until it finished.

When you see "Your app was successfully deployed", click on the "View" button.

You should see "Hello world.". Copy the website URL.

Tip: Install Heroku toolbelt to see your logs. Once you have Heroku toolbelt installed,
you can read the logs by::

   heroku logs -a <app name>


Add the GitHub Webhook
----------------------

Now we have a webservice, we can receive GitHub webhooks from it. Go to your
project settings on GitHub (not the "webservice" repo, the other one you created
earlier). I will use my own project, "strange-relationship".

In the Settings page, choose Webhooks, and click "Add webhook".

In the **Payload URL**, type in your webservice URL from Heroku.

Choose the **Content type** ``application/json``.

For the **Secret**, for security reasons, type in some random characters. Read up
more about `Securing your webhooks`_. **Take note of this secret token**.

.. _`Securing your webhooks`: https://developer.github.com/webhooks/securing/


Choose ``Let me select individual events.``. Check the following:

  - Issues
  - Issue comments
  - Pull requests

Press Add Webhook.

Update the Config Variables in Heroku
-------------------------------------

Almost ready to actually start writing bots! Let's go back to your Heroku dashboard
real quick.

Go to the **Settings** tab.

Click on the **Reveal Config Vars** button. Add two config variables here.

The first one called **GH_SECRET**. This is the secret webhook token you just created
a few moment ago.

The next one is called **GH_AUTH**. This is your GitHub personal access token,
which you used in the previous section, when we worked with gidgethub on the command
line.

Your first GitHub bot!
----------------------

Ok NOW everything is finally ready. Let's start with something simple. Let's have
a bot that **responds to every newly created issue in your project**. For example,
whenever someone creates an issue, the bot will automatically say something like:
"Thanks for the report, @user. I will look into this ASAP!"

Go to the ``__main__.py`` file, in your webservice codebase.

The first change the part where we did::

   app.router.add_get("/", main)

into::

   app.router.add_post("/", main)

This is because GitHub will send you **POST** requests to the webhook instead of **GET**.

Next, add the following imports::

   import aiohttp

   from aiohttp import web

   from gidgethub import routing, sansio
   from gidgethub import aiohttp as gh_aiohttp

   router = routing.Router()


Update the **main** coroutine, instead of printing out "Hello World"::

   async def main(request):
       # read the GitHub webhook payload
       body = await request.read()

       # our authentication token and secret
       secret = os.environ.get("GH_SECRET")
       oauth_token = os.environ.get("GH_AUTH")

       # a representation of GitHub webhook event
       event = sansio.Event.from_http(request.headers, body, secret=secret)

       # instead of mariatta, use your own username
       async with aiohttp.ClientSession() as session:
           gh = gh_aiohttp.GitHubAPI(session, "mariatta",
                                     oauth_token=oauth_token)

           # call the appropriate callback for the event
           await router.dispatch(event, gh)

       # return a "Success"
       return web.Response(status=200)

Add the following coroutine (above **main**)::

   @router.register("issues", action="opened")
   async def issue_opened_event(event, gh, *args, **kwargs):
       """ Whenever an issue is opened, greet the author and say thanks."""
       pass

This is where we are essentially subscribing to the GitHub ``issues`` event, and
specifically to the "opened" issues event.

The two important parameters here are: ``event`` and ``gh``.

``event`` here is the representation of GitHub's webhook event. We can access the
event payload by doing ``event.data``.

``gh`` is the gidgethub GitHub API, which we've used in the previous section to
make API calls to GitHub.

.. _greet_author:

Leave a comment whenever an issue is opened
'''''''''''''''''''''''''''''''''''''''''''

Back to the task at hand. We want to *leave a comment whenever someone opened an
issue*. Now that we're subscribed to the event, all we have to do now is to
actually create the comment.

We've done this in the previous section on the command line. You will recall
the code is something like the following::

   await gh.post(url, data={"body": message})

Let's think about the ``url`` in this case. Previously, you might have constructed
the url manually as follows::

   url = f"/repos/mariatta/strange-relationship/issues/{issue_number}/comments"

When we receive the webhook event however, the issue comment url is actually
supplied in the payload.

Take a look at GitHub's issue event payload `example
<https://developer.github.com/v3/activity/events/types/#webhook-payload-example-15>`_.

It's a big JSON object. The portion we're interested in is::

   {
     "action": "opened",
     "issue": {
       "url": ...,
       "comments_url": "https://api.github.com/repos/baxterthehacker/public-repo/issues/2/comments",
       "events_url": "...",
       "html_url": "...",
     ...
   }

Notice that ``["issue"]["comments_url"]`` is actually the URL for posting comments to
this particular issue. With this knowledge, your url is now::

   url = event.data["issue"]["comments_url"]

The next piece we want to figure out is what should the comment message be. For
this exercise, we want to greet the author, and say something like "Thanks @author!".

Take a look again at the issue event payload::

   {
     "action": "opened",
     "issue": {
       "url": "...",
        ...
       "user": {
         "login": "baxterthehacker",
         "id": ...,
     ...
   }

Did you spot it? The author's username can be accessed by ``event.data["user"]["login"]``.

So now your comment message should be::

   author = event.data["issue"]["user"]["login"]
   message = f"Thanks for the report @{author}! I will look into it ASAP! (I'm a bot)."


Piece all of that together, and actually make the API call to GitHub to create the
comment::

   @router.register("issues", action="opened")
   async def issue_opened_event(event, gh, *args, **kwargs):
       """ Whenever an issue is opened, greet the author and say thanks."""

       url = event.data["issue"]["comments_url"]
       author = event.data["issue"]["user"]["login"]

       message = f"Thanks for the report @{author}! I will look into it ASAP! (I'm a bot)."
       await gh.post(url, data={"body": message})


Your entire **__main__.py** should look like the following::

    import os
    import aiohttp

    from aiohttp import web

    from gidgethub import routing, sansio
    from gidgethub import aiohttp as gh_aiohttp

    router = routing.Router()

    @router.register("issues", action="opened")
    async def issue_opened_event(event, gh, *args, **kwargs):
        """
        Whenever an issue is opened, greet the author and say thanks.
        """
        url = event.data["issue"]["comments_url"]
        author = event.data["issue"]["user"]["login"]

        message = f"Thanks for the report @{author}! I will look into it ASAP! (I'm a bot)."
        await gh.post(url, data={"body": message})

    async def main(request):
        body = await request.read()

        secret = os.environ.get("GH_SECRET")
        oauth_token = os.environ.get("GH_AUTH")

        event = sansio.Event.from_http(request.headers, body, secret=secret)
        async with aiohttp.ClientSession() as session:
            gh = gh_aiohttp.GitHubAPI(session, "mariatta",
                                      oauth_token=oauth_token)
            await router.dispatch(event, gh)
        return web.Response(status=200)


    if __name__ == "__main__":
        app = web.Application()
        app.router.add_post("/", main)
        port = os.environ.get("PORT")
        if port is not None:
            port = int(port)

        web.run_app(app, port=port)


Commit that file, push it to GitHub, and deploy it in Heroku.

Once deployed, try and create an issue in the repo. See your bot in action!!

Congrats! You now have a bot in place! Let's give it another job.

.. _say_thanks:

Say thanks when an issue has been merged
''''''''''''''''''''''''''''''''''''''''

Let's now have the bot **say thanks, whenever a pull request has been merged**.

For this case, you'll want to subscribe to the ``pull_request`` event, specifically
when the ``action`` to the event is ``closed``.

For reference, the relevant GitHub API documentation for the ``pull_request`` event
is here: https://developer.github.com/v3/activity/events/types/#pullrequestevent.

The example payload for this event is here: https://developer.github.com/v3/activity/events/types/#webhook-payload-example-28

Try it on your own.

**Note**: A pull request can be closed without it getting merged. You'll need
a way to determine whether the pull request was merged, or simply closed.

.. _react_to_comments:

React to issue comments
'''''''''''''''''''''''

Everyone has opinion on the internet. Encourage more discussion by
**automatically leaving a thumbs up reaction** for every comments in the issue.
Ok you might not want to actually do that, (and whether it can actually encourage
more discussion is questionable). Still, this can be a fun exercise.

How about if the bot always gives **you** a thumbs up?

Try it out on your own.

- The relevant documentation is here: https://developer.github.com/v3/activity/events/types/#issuecommentevent

- The example payload for the event is here: https://developer.github.com/v3/activity/events/types/#webhook-payload-example-14

- The API documentation for reacting to an issue comment is here: https://developer.github.com/v3/reactions/#create-reaction-for-an-issue-comment

.. _label_prs:

Label the pull request
''''''''''''''''''''''

Let's make your bot do even more hard work. **Each time someone opens a pull request,
have it automatically apply a label**. This can be a "pending review" or
"needs review" label.

The relevant API call is this: https://developer.github.com/v3/issues/#edit-an-issue

