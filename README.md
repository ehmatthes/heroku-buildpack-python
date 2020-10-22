# Simplified version of the Heroku buildpack for Python 

This is a demonstration version of the Heroku Python Buildpack. Deploying a Django project to Heroku is much simpler than deploying to a VPS such as Linode or Digital Ocean, but it still requires a number of configuration steps that could be automated. This is particularly true for people new to deployment, and for the deployment of small, simple apps. The goal of this project is to have as few steps as possible between a small to medium size Django project that runs locally on SQLite, and runs sucessfully on Heroku.

The original vision of Heroku was to provide as seamless of a deployment process as possible, while not restraining developers. The goal of this revised buildpack is to automate initial configuration steps, but in a way that developers can then customize without having to undo any of the auto-configuration steps.

For a simple Django project, you can deploy your project in these steps (assuming you're already using Git to manage your project and you already have the [Heroku CLI](https://devcenter.heroku.com/articles/getting-started-with-python?singlepage=true#set-up) installed):

- Run `heroku create` from a terminal at the root project directory.
- Run `heroku buildpacks:set https://github.com/ehmatthes/heroku-buildpack-python.git`.
- Run `heroku config:set AUTCONFIGURE_ALL=1`.
- Run `git push heroku main`.
- Run `heroku run python manage.py migrate`.
- Run `heroku open`.

With these commands and no modification to your project itself, you should have a working project deployed to Heroku.

*Note: Don't use this for a production project yet. This is currently a demonstration project.*

# Motivation

Heroku has been a good option for deploying Django projects for a number of years now. Experienced Django developers can read through the Heroku docs and deploy their projects with little headache. But Django beginners, and people with less deployment experience, can run into trouble for a number of reasons:
- The Heroku documentation for initial deployment of Django projects is not always kept up to date.
- The official documentation recommends using the `django-heroku` project, which has been archived.
- The `django-heroku` library requires `psycopg2`, which now requires that you have Postgres installed.
  - Rather than update the project to allow for something like `psycopg2-binary`, which doesn't require having Postgres installed, the Heroku docs recommend that people install Postgres locally, even if they aren't planning to use Postgres locally.

To put it simply, there are a number of initial deployment steps that have relatively clear default configurations. The buildpack seems like the best place to make this default configuration, rather than a third-party package that's prone to abandonment or archiving. This project is meant as a demonstration of how effectively the buildpack could handle the simplest deployment cases, while still allowing for the more customized deployment configuration that experienced developers are looking for.

# Deploying a project

You should not be using this buildpack to deploy a production project. This is currently a demonstration project, and will be changed and possibly broken without warning. That said, I welcome people trying to deploy their projects with this buildpack, and reporting any [successes or failures](https://github.com/ehmatthes/heroku-buildpack-python/issues/10).

These steps assume you are already using Git to manage your project, and that you have the [Heroku CLI](https://devcenter.heroku.com/articles/getting-started-with-python?singlepage=true#set-up) installed. To deploy your project:

- Run `heroku create` from a terminal at the root project directory.
- Run `heroku buildpacks:set https://github.com/ehmatthes/heroku-buildpack-python.git`. This will tell Heroku to use this modified buildpack in place of the standard Heroku Python buildpack.
- Run `heroku config:set AUTCONFIGURE_ALL=1`. This tells this buildpack to automatically configure your project for deployment.
- Run `git push heroku main`. If your default branch is still `master`, use that name instead.
- Run `heroku open` to open your project in a browser.
- You will still need to use `heroku run python manage.py migrate` to migrate your database, or `heroku run bash` to log in to a console where you can run this and any other initial deployment commands.

If you are curious to try this process but don't have a small project to try it out on, you can use [this instance](https://github.com/ehmatthes/learning_log_heroku_test) of the Learning Log project from Python Crash Course. There is also a [separate version](https://github.com/ehmatthes/learning_log_heroku_test_pipfile) of the same project that uses Pipenv instead of *requirements.txt*.

# Technical Overview

This buildpack has been modified to do the following if an `AUTOCONFIGURE_ALL` environment variable has been set:
- Create a `Procfile` if there is none present.
- Add `gunicorn` to *requirements.txt* or *Pipfile* if it's not already listed.
- Configure `ALLOWED_HOSTS` so the project will run on Heroku. Currently this is set to `['.herokuapp.com']`.
- Configure the database:
  - Add `psycopg2` and `dj-database-url` to *requirements.txt* or *Pipfile*, if they're not already listed.
  - Configure *settings.py* to use Heroku's Postgres database.
- Configure static files:
  - Add `whitenoise` to *requirements.txt* or *Pipfile* if it's not already listed.
  - Add `whitenoise` to middleware.
  - Create a directory for static files.

All of the Heroku-specific deployment configuration is done in an `autoconfigure` script, which you can see [here](https://github.com/ehmatthes/heroku-buildpack-python/blob/main/bin/steps/autoconfigure). Please forgive my rusty Bash code. :/

# Documentation
- [Merging upstream changes](https://github.com/ehmatthes/heroku-buildpack-python/blob/main/my_docs/merging_upstream.md)

# Anticipated changes

This should probably not be the default Python buildpack behavior, so it would need config variables to trigger auto-configuration. I would anticipate having a set of config variables, such as `AUTO_CONFIGURE_ALL`, `AUTO_CONFIGURE_DB`, `AUTO_CONFIGURE_STATIC`, and maybe one or two more if needed. This way, as a user begins to customize their deployment, they can turn any or all of these flags off and customize each aspect of deployment.

I don't know if this is all specific to Django, or if it applies to other Python projects as well. If this is all Django-specific, better names might be `AUTOCONFIGURE_ALL_DJANGO` or `AUTOCONFIGURE_DJANGO_ALL`. 

# Disclaimer

I am the author of [Python Crash Course]() from [No Starch Press](), and one of the projects in the book involves building a simple Django project and deploying the project to Heroku. I am deeply appreciative of the service that Heroku has offered to Django developers over the years. I would love to make the deployment process simpler for my readers, but also for anyone who is new to Django, or who is looking for the simplest, reliable deployment process possible.

I have answered readers' basic deployment questions for years now, but I don't have significant experience with deploying a wide range of projects, or significantly complex projects. If I have made any incorrect assumptions, or missed anything else of significance, I would love to know. If you want to share feedback, please either open an issue or reach out on [Twitter](https://twitter.com/ehmatthes/) or email (ehmatthes at gmail).