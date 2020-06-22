# Welcome to Quokka Project documentation
<hr>

<center>
![logo](img/emoji_small.png)
</center>

This docs covers the development version of Quokka CMS and is always subjected to changes, if you want previous docs versions use github tags.

Access our website to see more details: [http://quokkaproject.org/](http://quokkaproject.org/)

Quokka is an open source project and it is available at [https://github.com/quokkaproject/quokka](https://github.com/quokkaproject/quokka).

## What is Quokka CMS, the happiest CMS in the world?
<hr>

Quokka CMS is a Content Management System, a platform to publish blogs, websites, portals and also a nice solution for intranets, Document Management System (GED/ECM). It is even possible to create various kinds of applications based in content publishing as fundraising and e-commerce applications.

Quokka is Open Source developed in Python using Flask and MongoDB as its base.

Quokka is not limited to CMS area, it is possible to create Quokka extensions to provide any kind of web application based on Python and Flask.

Optionally, Quokka can generate a static website from the contents generated in its admin interface.

## Features
<hr>

- Web based content management admin interface
- Multiple content formats (markdown, rst, html, plaintext)
- Compatibility with any of the Pelican Themes
- Flat file NoSQL database TinyDB or optionally MongoDB for scale deployments
- Host the Quokka on server or generate a static website
- Extensible via modules/plugins
- Powered by Python, Flask, Flask-Admin, TinyMongo and Pelican Themes

## Installation
<hr>

### Demo

> Use this if you want to see how Quokka works (you need to have docker installen in your machine)

```
docker run --rm -it -p 5000:5000 quokka/quokka
```

Go to `http://localhost:5000/admin` and login using admin/admin.


### Installing Quokka from Pypi
> NOTE: QuokkaCMS requires Python 3.6+

```
python3 -m venv venv
. venv/bin/activate
pip3 install quokka
```

### Installing and running for development mode

> NOTE: Quokka CMS requires Python 3.6+

```
git clone https://github.com/quokkaproject/quokka
cd quokka
python3 -m venv venv
. venv/bin/activate
make install
make devserver
```

## Using Quokka CMS

Start a project

```bash
quokka init NewWebsite --theme=flex --modules=gitpages,heroku
```


```
🐹 Quokka project created 🐹
📝 Name: NewWebsite
📁 Location: /tmp/newwebsite
📚 Template: default
🎨 Themes: flex theme installed
🚚 Modules: [gitpages, heroku] installed
🔧 Config: Config file written in /tmp/newwebsite/quokka.yml
➡ Go to /tmp/newwebsite
⚙ run `quokka runserver` to start!
📄 Check the documentation on http://quokkaproject.org
🐹 Happy Quokka! 🐹
```

> YES! it outputs emojis 🐹

The above command will generate your project in `NewWebsite` folder as:

```bash
.
├── databases        # TinyDB database files (gitignored)
├── modules          # Custom modules to load on EXTRA_EXTENSIONS
├── static_build     # output static site
├── themes           # Front-end Themes (Pelican and Quokka Themes supported)
├── uploads          # Media uploaded via admin
├── .gitignore       # gitignore to exclude sensitive files
├── quokka.yml       # Project settings
├── .secrets.yml     # To store keys, tokens and passwords (gitignored)
└── wsgi.py          # To deploy `gunicorn wsgi:app`
```

You can optionally pass arguments:

Choose existing theme (the default is [Malt](https://github.com/grupydf/malt))

```bash
quokka init NewWebsite --theme http://github.com/<user>/theme
```

Install modules

```bash
quokka init NewWebsite --theme http://github.com/<user>/theme --modules="commerce,foo"
```

> the above looks for `quokka_commerce` and `quokka_foo` in PyPI and installs it

#### Set important configurations

```bash
quokka init NewWebsite --theme http://github.com/user/theme --config="auth_enabled=false"
```

> That is optional, you can to edit `quokka.yml` to tune your settings.

#### Create an admin user
```
quokka adduser
```

#### Run your website

```bash
quokka runserver --port 5000
```

#### Access admin interface

```bash
http://localhost:5000/admin
```

#### Access your site

```bash
http://localhost:5000
```

## Deploy

### You can deploy your Quokka Website in a WSGI server

Check the `wsgi.py` and refer to it when deploying in wsgi servers.

```bash
cd myproject
gunicorn wsgi:app -w 4 -b "0.0.0.0:8000"
```

An example of `supervisord` config

```ini
[program:quokka]
command=/myproject/venv/bin/gunicorn wsgi:app -w 4 -b "0.0.0.0:8000"
directory=/myproject
```

For more information read [Gunicorn documentation](http://docs.gunicorn.org/en/stable/index.html)

## Publish Static HTML website

> **NOTE**: To generate a static website all user management, keys and passwords will be removed from settings.

### You can generate a static HTML website to host anywhere

Once you have your website running locally you can easily generate a
static HTML website from it.

```bash
$ quokka publish --static [--output path]
Generating static HTML website on ./static_build folder
```

Once you have a ./static_build folder populated with static website you can deploy it
using SCP, FTP or git, it is a full static website.

### Deploying to github pages from command line

> NOTE: You need either ssh key access to github or it will ask login/password

```bash
quokka publish --static --git=<user>/<mysite> --branch=gh_pages
```

> The above is also available in admin under 'publish' menu.

### Deploying via SCP

```bash
$ quokka publish --static --scp --dest='me@hostname:/var/www/mysite' [--sshkey ~/.ssh/key] [--password xyz]
password: ...
```

### Deploying to Heroku

> This requires `heroku` client installed, if `Procfile` is not found it will be generated

```bash
quokka publish --static --heroku --options
```

### Deploying via FTP

```bash
quokka publish --static --ftp --host='ftp://server.com' --dest='/var/www/mysite'
```

### Load database from remote deployment (only for TinyDB)

When you publish a static website along with the static files the database also
goes to the server under the databases/ folder only as a backup and snapshot.

You can load that remote database locally e.g: to add new posts and then re-publish

```bash
$ quokka restoredb --remote --git=rochacbruno/mysite
Creating a backup of local database...
Downloading remote database
Restoring database..
Done...
```

Now you can run `quokka runserver` open your `localhost:5000/admin` write new content
and then `Publish` website again using command line or admin interface.

> NOTE: If you want to restore a local database use `--local` and `--path path/to/db`

## Using MongoDB

You can choose to use MongoDB instead of TinyDB, That is useful specially if
you deploy or local instance has more than one admin user concurrently
and also useful if you want to install plugins which support MongoDB only
(because it relies on aggregations and gridfs)

You only need a running instance
of Mongo server and change `quokka.yml:DB` on your project from:

```yaml
quokka:
  DB:
    system: tinydb
    folder: databases
```

to:

```yaml
quokka:
  DB:
    system: mongodb
    name: my_database
    host: 127.0.0.1
    port: 2600
```

Then when running `quokka` again it will try to connect to that Mongo Server.

With that you can deploy your site on `wsgi` server or can also generate `static` website.

### Running mongo in a Docker container

```bash
cd <your quokka project folder>
docker run -d -v $PWD/databases:/data/db -p 27017:27017 mongo
# wait some seconds until mongo is started
quokka runserver
```

## Contributing to Quokka CMS Development

Do you want to be part of this open-source project?

Take a look at [Contributing Guidelines](https://github.com/quokkaproject/quokka/blob/master/CONTRIBUTING.md)

### Setup a contributor environment

Ensure you have `Python3.6+` fork this repo and:

```bash
git clone https://github.com/$YOURNAME/quokka
cd quokka

# create a Python3.6 virtual env
make create_env

# activate the venv
. venv/bin/activate

# install Quokka in --editable mode (using pbr)
make install

# Create a new admin user
make adduser

# run quokka
make devserver
```

Access `http://localhost:5000/admin` and `http://localhost`


## Roadmap

This list is available on [Github](https://github.com/quokkaproject/quokka/issues).

This is the list of tasks to be completed until `1.0.0` can be released.

Support 100% coming only for `malt` and `bootstrap3` themes


## Screenshots

### The main Admin page

![home cms](https://user-images.githubusercontent.com/458654/35923897-a6f321be-0c08-11e8-95ff-5510862326ea.png)


### [Pelican themes](http://www.pelicanthemes.com/) compatible

An article showing in Malt theme  

![start contributing to quokka project my site](https://user-images.githubusercontent.com/458654/35923881-a128c9d2-0c08-11e8-94b4-813588209f04.png)

The same article using Bootstrap 3 theme  

![start contributing to quokka project my site2](https://user-images.githubusercontent.com/458654/35924086-32840ebe-0c09-11e8-861e-db1a66b87326.png)

> See more screenshots on [Github](https://github.com/quokkaproject/quokka/issues/647).


> [Start contributing right now!](https://github.com/quokkaproject/quokka/blob/master/CONTRIBUTING.md)