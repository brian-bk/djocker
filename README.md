# Djocker

Django's template contains a lot of boilerplate code, but here in
addition to the boilerplate code is boilerplate infrastructure-as-code
(hope you like buzzwords!).

This template is just like django's default project template,
in that it is NOT at all production ready. You have to learn
enough about django and docker to run this in a production
environment.

## Changes and Additions

You can easily compare 
[all changes](https://github.com/brian-bk/djocker-template/compare/586d17481b922f16a7ae0fd8ed62290e8a837e5f...master)
from the base django-admin template.

Note that because of the ease containerization provides to add diverse
resources, this project sets up postgres instead of sqlite and additionally
sets up a celery container ready to run asynchronous tasks independently
of the webapp. Rather than run just the web application you can run
the database, broker, worker, web application, and temporarily the
database migrations all at once.

## Setting up a project

#### Download static docker configuration

First, download the static 
[configuration](https://github.com/brian-bk/djocker-conf)
pieces outside the realm of the django template.
```
### Change example_project to your project name

curl -L https://github.com/brian-bk/djocker-conf/archive/master.tar.gz | tar xvz
# Move directory to your project name
mv djocker-conf-master example_project && cd example_project
```

#### Set up virtualenv and install django

Install the same specific django version used in this project to
a virtual environment. This step is optional if you just want to
install django directly.
```
### Copy+paste

# Create a virtualenv (always best practice for local development)
# and activate it
virtualenv --python python3 venv
. ./venv/bin/activate

# Install django to use django-admin
# If you want to install everything just install requirements.txt,
# for IDEs and such to pick up your requirements.
pip install -r django_requirement.txt
```

#### Create django app

Create the django project in app/ from the
[django template](https://github.com/brian-bk/djocker-template/)
and move dynamic docker-configuration back to the base project directory.
```
### Copy+paste

# Start the django project
mkdir app && django-admin startproject \
  --template=https://github.com/brian-bk/djocker-template/archive/master.zip \
  --extension=py,env,yml \
  $(basename $(pwd)) \
  app

# Move docker configuration to current directory
mv app/secret.env . && mv app/docker-compose.yml .
```

#### All done!

You are now ready to test the app. Note that this docker-compose is designed
for local development and will mount the app/ directory - this is not
needed in a production setting. In addition, docker secrets would provide
a better location to store the secret.env variables.

You're ready to try out django running in docker! Start up docker-compose
and visit http://localhost:8000. You should see 5 containers start up,
eventually down to 4 when migrations are complete.
```
sudo docker-compose up --build
```

