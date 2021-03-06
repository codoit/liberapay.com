# Liberapay

[![Build Status](https://travis-ci.org/liberapay/liberapay.com.svg?branch=master)](https://travis-ci.org/liberapay/liberapay.com)
[![Weblate](https://hosted.weblate.org/widgets/liberapay/-/shields-badge.svg)](https://hosted.weblate.org/engage/liberapay/?utm_source=widget)
[![Donate](https://liberapay.com/assets/widgets/donate.svg)](https://liberapay.com/liberapay/donate)

[Liberapay](http://liberapay.com) is a recurrent donations platform.

It's a fork of Gittip/Gratipay, see [this post](https://github.com/liberapay/salon/issues/8) for the differences between the two.

## Contact

You have a question? Come ask us in [the salon](https://github.com/liberapay/salon) or in the IRC channel #liberapay on [Freenode](http://webchat.freenode.net/).


## Contributing to the translations

[![We use Weblate for translations](https://hosted.weblate.org/widgets/liberapay/-/287x66-white.png)](https://hosted.weblate.org/engage/liberapay/?utm_source=widget)

If you have questions about translating Liberapay, you can ask them [in the translation thread](https://github.com/liberapay/salon/issues/2) of the salon.


## Contributing to the code

### Introduction

Liberapay is a fork of [Gratipay](https://github.com/gratipay/gratipay.com), so it uses the web micro-framework [Aspen](http://aspen.io/), which is based on filesystem routing and [simplates](http://simplates.org/). Don't worry, it's quite simple. For example to make Liberapay return a `Hello $user, your id is $userid` message for requests to the URL `/$user/hello`, you only need to create the file `www/%username/hello.spt` with this inside:

```
from liberapay.utils import get_participant
[---]
participant = get_participant(state)
[---] text/html
{{ _("Hello {0}, your id is {1}", request.path['username'], participant.id) }}
```

As illustrated by the last line our default template engine is [Jinja](http://jinja.pocoo.org/).

The `_` function attempts to translate the message into the user's language and escapes the variables properly (it knows that it's generating a message for an HTML page).

The python code inside simplates is only for request-specific logic, common backend code is in the `liberapay/` directory.

### Installation

Firstly, make sure you have the following dependencies installed:

- python ≥ 2.7.8 ([we're working on porting to python 3](https://github.com/liberapay/liberapay.com/pull/88))
- postgresql 9.4.5 (see [the official download & install docs](https://www.postgresql.org/download/)
- make

Then run:

    make env

Now you need to give yourself superuser postgres powers (if it hasn't been done already), and create two databases:

    su postgres -c "createuser --superuser $(whoami)"

    createdb liberapay
    createdb liberapay_tests

If you need a deeper understanding take a look at the [Database Roles](https://www.postgresql.org/docs/9.4/static/user-manag.html) and [Managing Databases](https://www.postgresql.org/docs/9.4/static/managing-databases.html) sections of PostgreSQL's documentation.

Then you can set up the DB:

    make schema

### Configuration

Environment variables are used for configuration, the default values are in
`defaults.env` and `tests/test.env`. You can override them in
`local.env` and `tests/local.env` respectively.

### Running

Once you've installed everything and set up the database, you can run the app:

    make run

It should now be accessible at [http://localhost:8339/](http://localhost:8339/).

You can create some fake users to make it look more like the real site:

    make data

### SQL

The python code interacts with the database by sending raw SQL queries through
the [postgres.py](https://postgres-py.readthedocs.org/en/latest/) library.

The [official PostgreSQL documentation](https://www.postgresql.org/docs/9.4/static/index.html)
is your friend when dealing with SQL, especially the sections "[The SQL Language]
(https://www.postgresql.org/docs/9.4/static/sql.html)" and "[SQL Commands]
(https://www.postgresql.org/docs/9.4/static/sql-commands.html)".

The DB schema is in `sql/schema.sql`, but don't modify that file directly,
instead put the changes in `sql/branch.sql`. During deployment that script will
be run on the production DB and the changes will be merged into `sql/schema.sql`.
That process is semi-automated by `release.sh`.

### CSS and JavaScript

For our styles we use [SASS](http://sass-lang.com/) and [Bootstrap 3]
(https://getbootstrap.com/). Stylesheets are in the `style/` directory and our
JavaScript code is in `js/`. Our policy for both is to have as little as
possible of them: the website should be almost entirely usable without JS, and
our CSS should leverage Bootstrap as much as possible instead of containing lots
of custom rules that would become a burden to maintain.

We compile Bootstrap ourselves from the SASS source in the `style/bootstrap/`
directory. We do that to be able to easily customize it by changing values in
`style/variables.scss`. Modifying the files in `style/bootstrap/` is probably
not a good idea.

### Testing [![Build Status](https://travis-ci.org/liberapay/liberapay.com.svg)](https://travis-ci.org/liberapay/liberapay.com)

The easiest way to run the test suite is:

    make test

That recreates the test DB's schema and runs all the tests. To speed things up
you can also use the following commands:

- `make pytest` only runs the python tests without recreating the test DB
- `make pytest-re` does the same but only runs the tests that failed in the previous run

### Tinkering with payments

We depend on [MangoPay](https://www.mangopay.com/) for payments. If you want to modify that part of the code you'll need the [MangoPay API documentation](https://docs.mangopay.com/api-references/).

### Deploying the app

Liberapay is hosted on [OpenShift Online](https://openshift.com/), which runs
the [OpenShift M4][M4] platform (also called OpenShift 2.x, not to be confused
with the newer OpenShift 3.x based on Docker). The user documentation is on
[developers.openshift.com][OS-dev].

To deploy the app simply run `release.sh`, it'll guide you through it.

[M4]: https://docs.openshift.org/origin-m4/
[OS-dev]: https://developers.openshift.com/


## License

[CC0 Public Domain Dedication](http://creativecommons.org/publicdomain/zero/1.0/)
