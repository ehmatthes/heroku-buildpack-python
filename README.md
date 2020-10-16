# Demonstration version of Heroku Buildpack: Python

This is a demonstration version of the Heroku Python Buildpack. Deploying a Django project to Heroku is much simpler than deploying to a VPS such as Linode or Digital Ocean, but it still requires a number of configuration steps that could be automated. This is particularly true for people new to deployment, and for the deployment of small, simple apps.

This buildpack has been modified to do the following if an `AUTOCONFIGURE_ALL` environment variable has been set:
- Create a `Procfile` if there is none present.
- Add `gunicorn` to *requirements.txt* if it's not already listed.
- Configure `ALLOWED_HOSTS` so the project will run on Heroku. Currently this is set to `['*']`.
- Configure the database:
  - Add `psycopg2` and `dj-database-url` to *requirements.txt*, if they're not already listed.
  - Configure *settings.py* to use Heroku's Postgres database.
- Configure static files:
  - Add `whitenoise` to *requirements.txt* if it's not already listed.
  - Configure settings for static files.
  - Add `whitenoise` to middleware.
  - Create a directory for static files.

# Motivation

Heroku has been a good option for deploying Django projects for a number of years now. Experienced Django developers can read through the Heroku docs and deploy their projects with little headache. But Django beginners, and people with less deployment experience, can run into trouble for a number of reasons:
- The Heroku documentation for initial deployment of Django projects is not always kept up to date.
- The official documentation recommends using the `django-heroku` project, which has been archived.
- The `django-heroku` library requires `psycopg2`, which now requires that you have Postgres installed.
  - Rather than update the project to allow for something like `psycopg2-binary`, which doesn't require having Postgres installed, the Heroku docs recommend that people install Postgres locally, even if they aren't planning to use Postgres locally.

To put it simply, there are a number of initial deployment steps that have relatively clear default configurations. The buildpack seems like the best place to make this default configuration, rather than a third-party package that's prone to abandonment or archiving. This project is meant as a demonstration of how effectively the buildpack could handle the simplest deployment cases, while still allowing for the more customized deployment configuration that experienced developers are looking for.

# Deploying your (test) project

You should not be using this buildpack to deploy a production project. This is an experimental demonstration project, and will be changed and possibly broken without warning. That said, I welcome people trying to deploy their projects with this buildpack, and reporting any [successes or failures](https://github.com/ehmatthes/heroku-buildpack-python/issues/10).

These steps assume you are already using Git to manage your project, and that you have the [Heroku CLI]() installed. To deploy your project:

- Run `heroku create` from a terminal at the root project directory.
- Run `heroku buildpacks:set https://github.com/ehmatthes/heroku-buildpack-python.git#simplify_deploy`. This will tell Heroku to use this modified buildpack in place of the standard Heroku Python buildpack.
- Run `heroku config:set AUTCONFIGURE_ALL=1`. This tells this buildpack to automatically configure your project for deployment.
- Run `git push heroku master`.
- Run `heroku open` to open your project in a browser.
- You will still need to use `heroku run python manage.py migrate` to migrate your database, or `heroku run bash` to log in to a console where you can run this and any other initial deployment commands.



# Disclaimer

I am the author of [Python Crash Course]() from [No Starch Press](), and one of the projects in the book involves building a simple Django project and deploying the project to Heroku. I am deeply appreciative of the service that Heroku has offered to Django developers over the years. I would love to make the deployment process simpler for my readers, but also for anyone who is new to Django, or who is looking for the simplest, reliable deployment process possible.

I have answered readers' basic deployment questions for years now, but I don't have significant experience with deploying a wide range of projects, or significantly complex projects. If I have made an incorrect assumption, or missed anything else of significance, I would love to know. If you want to share feedback, please either open an issue or reach out on Twitter or email.