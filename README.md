# NAME

Panky - Panky is a chatty, github, issue, and-ci helper bot for your team

# VERSION

version 0.001

# SYNOPSIS

\[!\[Build Status\](https://secure.travis-ci.org/throughnothing/Panky.png?branch=master)\](http://travis-ci.org/throughnothing/Panky)

Panky is a chatting, github, Jira, jenkins loving
web-app/bot/do-it-all/chef(?) for your team.

Panky lurks in your teams chat room (Jabber is currently supported) and provides
useful information and functionality __all day long__.

__Note: [Panky](https://metacpan.org/pod/Panky) is still in active development and is not feature complete__

Currently, Panky will connect to your chat server, update you about what's
going on with your github repos (new pushes, pull request activity, comments,
etc.) and enable you to get info about them on demand.  It can also parse
`Jira` links, and provide information about (and start) `Jenkins` builds.

Panky can also use the `Github|http://github.com`
[commit status API](https://github.com/blog/1227-commit-status-api) to show
the status of your Continuous Integration builds on Pull Requests.

Some sample usage:

    # Panky sets up github hooks for itself for that repo
    # Of course your github user must have admin access to the repo in question
    # For this to work
    > panky: gh setup repo1/user1

    # Set an alias 'myrepo' for user/my-repo
    > panky: gh set repo myrepo => user/my-repo
    # Link the github repo 'user/my-repo' with the jenkins job 'ci-job-name'
    > panky: ci set repo user/my-repo => ci-job-name

    # Run the 'ci-job-name' job against pull-request #1 on user/my-repo
    > panky: test my-repo pr 1
    # Run the 'ci-job-name' job against commit abc123
    > panky: test my-repo abc123

    # List all pull-requests for a repo
    > panky: gh prs myrepo
    > <panky> 1: Fix the broken things http://git.io/XXX
    > <panky> 2: Fix the other broken things http://git.io/XXXX

    # When a build succeeds/fails
    > <panky> [Jenkins: ci-job-name] failed https://myjenkins/job/ci-job-name/1

    # When a teammate creates a pull request (with git.io shortened url)
    > <panky> [user/my-repo] PR 'Fix the broken things' opened by throughnothing http://git.io/XXXX

    # Panky can show info about your JIRA tickets
    > hey, check out https://company.atlassian.net/brows/PROJ-1
    > <panky> [PROJ-1](Priority) assignee => Issue summary

# INSTALLING

[Panky](https://metacpan.org/pod/Panky) requires a non-blocking server in order to run.  This means that
you probably want to use either [Twiggy](https://metacpan.org/pod/Twiggy), or the builtin [Mojolicious](https://metacpan.org/pod/Mojolicious)
server.

To install the dependencies for [Panky](https://metacpan.org/pod/Panky), simply run:

    cpanm --installdeps .

If you're using OSX, there's a good chance [EV](https://metacpan.org/pod/EV) will fail to install.  A nasty
workaround for this for now, is to download the latest EV package tarball
(link can be found here: https://metacpan.org/module/EV ), and modify the
`Makefile.PL`.  Find the `WriteMakefile` section near the bottom, and add:

    CC => 'gcc',

to its outermost arguments list.  You should get something like:

    WriteMakefile(
        dist => { ... },
        depend => { ... },
        CC => 'gcc',
        INC       => "-Ilibev",
        DEFINE    => "$DEFINE",
        NAME => "EV",

Once you've done that, you can \`cpanm .\` from inside the code directory to
install it.  After that, the rest of the dependencies should install fine
on OS X using \`cpanm --installdeps .\`

## Environment Variables

[Panky](https://metacpan.org/pod/Panky) is configured via environment variables to make it easy to install on
systems like [Heroku](http://heroku.com).

The following environment variables must be set for [Panky](https://metacpan.org/pod/Panky) to run:

- PANKY\_BASE\_URL

    This should be set to the base url that the [Panky](https://metacpan.org/pod/Panky) server will be running on.

- PANKY\_GITHUB\_USER

    The username of a Github user that will have access to whatever is needed.

- PANKY\_GITHUB\_PWD

    The Github password for the user mentioned above.

- PANKY\_CHAT\_JABBER\_JID

    The `jid` of [Panky](https://metacpan.org/pod/Panky)'s jabber account.

- PANKY\_CHAT\_JABBER\_PWD

    The password for [Panky](https://metacpan.org/pod/Panky)'s jabber account.

- PANKY\_CHAT\_JABBER\_HOST

    If you need to set your jabber host to something different than the domain
    part of the `jid`, then you can use this variable to do so.

- PANKY\_CHAT\_JABBER\_ROOM

    The jabber conference room that [Panky](https://metacpan.org/pod/Panky) should join.  This should be the
    full `jid` of the room, such as `room@conference.jabber.server.com`.

## Jenkins Support

You can also give it the `URL` to your [Jenkins](http://jenkins-ci.org) server
via the `PANKY_JENKINS_URL` option.  [Panky](https://metacpan.org/pod/Panky) will use this to generate
links to Jenkins builds, etc.  If you want [Panky](https://metacpan.org/pod/Panky) to be able to start builds
on jenkins (from pull requests etc.) you should pass `PANKY_JENKINS_USER` and
`PANKY_JENKINS_TOKEN` for authentication.

## JIRA Support

[Panky](https://metacpan.org/pod/Panky) can also work with JIRA if you have that.  You can enable JIRA support
by setting the following environment variables:

- PANKY\_JIRA\_URL

    The url of your jira server: `https://company.atlassian.net/`

- PANKY\_JIRA\_USER

    The username to use to authenticate with your JIRA server.

- PANKY\_JIRA\_PWD

    The password of the user used to authenticate with your JIRA server.

## Heroku

To run [Panky](https://metacpan.org/pod/Panky) in [Heroku](http://heroku.com), the easiest way is to use
the [Perloku](https://github.com/judofyr/perloku) buildpack.

    $ git clone https://github.com/throughnothing/Panky
    $ cd Panky
    $ heroku create -s cedar --buildpack http://github.com/judofyr/perloku.git
    # Optionally, if you want persistent storage across app restarts
    $ heroku addons:add heroku-postgresql(:dev)

Now your heroku app is setup and ready to receive the [Panky](https://metacpan.org/pod/Panky) application.
Before you push [Panky](https://metacpan.org/pod/Panky) to your app, you'll want to setup the environment
variables described above by using:

    $ heroku config:add ENVIRONMENT_VARIABLE="value"

For each config value that you wish to set.

Once all of that is set up, you can deploy the app using:

    $ git push heroku master

This will deploy your app code, install all dependencies, and run it.

If you have the PostgreSQL addon enabled, [Pany](https://metacpan.org/pod/Pany) will detect the
`DATABASE_URL` environment variable present, and use the PostgreSQL server,
otherwise it falls back to sqlite storage, which will get lost whenever
you restart your app.

# USAGE

Once configured and setup, [Panky](https://metacpan.org/pod/Panky) is mostly interacted with via chat
(jabber by default).  Below are some commands that [Panky](https://metacpan.org/pod/Panky) accepts.  In
general, these commands must be directed at the [Panky](https://metacpan.org/pod/Panky) chat bot
(i.e you must mention the bot by name: "panky: COMMAND").

- gh setup _FULL\_REPO\_NAME_

    This will direct [Panky](https://metacpan.org/pod/Panky) to setup [Github](http://github.com) Hooks for the
    repo in question. _FULL\_REPO\_NAME_ should look like `user/repo` or
    `organization/repo`.  The `GITHUB_USER` that is setup for [Panky](https://metacpan.org/pod/Panky) must have
    access to the repo if it is private for this to work.

# AUTHOR

William Wolf <throughnothing@gmail.com>

# COPYRIGHT AND LICENSE



William Wolf has dedicated the work to the Commons by waiving all of his
or her rights to the work worldwide under copyright law and all related or
neighboring legal rights he or she had in the work, to the extent allowable by
law.

Works under CC0 do not require attribution. When citing the work, you should
not imply endorsement by the author.
