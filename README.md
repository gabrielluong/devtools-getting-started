# Firefox Developer Tools

# Tasks to Complete
* [Create a Mozilla Bugzilla Account](#create-a-mozilla-bugzilla-account)
* [Setup Development Environment](#setup-development-environment)
* Join Slack for Communication - [Firefox DevTools Slack](https://devtools-html-slack.herokuapp.com/)
* Setup ESLint for your Text Editor - [ESLint Setup Guide](https://wiki.mozilla.org/DevTools/CodingStandards#JS_linting_with_ESLint)

# Create a Mozilla Bugzilla Account

Create an account on https://bugzilla.mozilla.org/. Mozilla’s Bugzilla is where you go to report bugs, usability papercuts, request features, etc. If you were to file a bug issue for Firefox Developer Tools, you would go to the [Developer Tools Bug Component](https://bugzilla.mozilla.org/enter_bug.cgi?product=Firefox&component=Developer%20Tools), select the relevant tool component and enter the bug title and description.

# Setup Development Environment

## Getting the Source

If you are building on Windows, consult this guide to get the required tools - [Building Firefox On Windows](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Build_Instructions/Windows_Prerequisites). We will be using a git workflow compare to what is mentioned in the Building Firefox On Windows guide, so come back when you reach "Getting The Source" in that guide.

The general guide for git workflow for Firefox (Gecko is the name of the layout engine) development can be found on this wiki:
[Git Workflow Guide](https://github.com/glandium/git-cinnabar/wiki/Mozilla:-A-git-workflow-for-Gecko-development).

In the guide above you will see the following tasks (this is a simplified version but you should still follow along the wiki above):
* Use your preferred package manager (Homebrew for Mac, Installers for Window) for installing the following:
  * Installing [Mercurial (hg)](https://www.mercurial-scm.org/wiki/Download)
  * Installing [Git](https://git-scm.com/downloads)
* Installing [git-cinnabar](https://github.com/glandium/git-cinnabar)
    * This is a Git wrapper around Mercurial to help us work around Firefox' Mercurial repositories which is where the source code lives.
* Setup your local Git repository to pull down the source code. The following command simply setups a local Git respository. You can read about the details of each command in the wiki guide above, but for the tl;dr:
```
git init gecko
cd gecko
git config fetch.prune true
git config push.default upstream
git remote add mozilla hg::https://hg.mozilla.org/mozilla-unified -t bookmarks/central
git remote set-url --push mozilla hg::ssh://hg.mozilla.org/integration/mozilla-inbound
git config remote.mozilla.fetch +refs/heads/bookmarks/*:refs/remotes/mozilla/*
git remote add try hg::https://hg.mozilla.org/try
git config remote.try.skipDefaultUpdate true
git remote set-url --push try hg::ssh://hg.mozilla.org/try
git config remote.try.push +HEAD:refs/heads/branches/default/tip
```
* Fetch the source code. This will take awhile and can fail because of a number of reasons - bandwidth, connection disconnected, ran out of space, etc.
```
git remote update
```
* By now, you won't actually see any files in your gecko folder. You need to create a branch to actually checkout the source code. mozilla/bookmarks/central represent the development branch that we will be branching off from. Think of it as the equivalent of master.
```
git checkout -b bugxxxxxxx mozilla/bookmarks/central  #<xxxxxxx> would be the bug number so just enter anything for now.
```
* You just finished grabbing the source code!

## Building Firefox

* Run the following command to download and setup all the build prerequisties to build Firefox.
```
./mach bootstrap
```
  * You can view my responses to the ./mach bootstrap configuration prompt:
```
Note on Artifact Mode:

Firefox for Desktop and Android supports a fast build mode called
artifact mode. Artifact mode downloads pre-built C++ components rather
than building them locally, trading bandwidth for time.

Artifact builds will be useful to many developers who are not working
with compiled code. If you want to work on look-and-feel of Firefox,
you want "Firefox for Desktop Artifact Mode".

Similarly, if you want to work on the look-and-feel of Firefox for Android,
you want "Firefox for Android Artifact Mode".

To work on the Gecko technology platform, you would need to opt to full,
non-artifact mode. Gecko is Mozilla's web rendering engine, similar to Edge,
Blink, and WebKit. Gecko is implemented in C++ and JavaScript. If you
want to work on web rendering, you want "Firefox for Desktop", or
"Firefox for Android".

If you don't know what you want, start with just Artifact Mode of the desired
platform. Your builds will be much shorter than if you build Gecko as well.
But don't worry! You can always switch configurations later.

You can learn more about Artifact mode builds at
https://developer.mozilla.org/en-US/docs/Artifact_builds.

Please choose the version of Firefox you want to build:
1. Firefox for Desktop Artifact Mode
2. Firefox for Desktop
3. Firefox for Android Artifact Mode
4. Firefox for Android
Your choice: 1

Looks like you have Homebrew installed. We will install all required packages via Homebrew.

Your version of Mercurial (4.3.1) is sufficiently modern.
Your version of Python (2.7.13) is new enough.
Your version of Rust (1.19.0) is new enough.
Rust supports x86_64-apple-darwin targets.

Mozilla recommends a number of changes to Mercurial to enhance your
experience with it.

Would you like to run a configuration wizard to ensure Mercurial is
optimally configured?

  1. Yes
  2. No

Please enter your reply: 1
================================================================================
Ensuring https://hg.mozilla.org/hgcustom/version-control-tools is up to date at /Users/oracle/.mozbuild/version-control-tools
pulling from https://hg.mozilla.org/hgcustom/version-control-tools
searching for changes
adding changesets
adding manifests
adding file changes
added 274 changesets with 691 changes to 338 files
updating bookmark @
(run 'hg update' to get a working copy)
335 files updated, 0 files merged, 5 files removed, 0 files unresolved
================================================================================
This wizard will guide you through configuring Mercurial for an optimal
experience contributing to Mozilla projects.

The wizard makes no changes without your permission.

To begin, press the enter/return key.

Enable history rewriting commands (Yn)?  n
The fsmonitor extension integrates the watchman filesystem watching tool
with Mercurial. Commands like `hg status`, `hg diff`, and `hg commit`
(which need to examine filesystem state) can query watchman to obtain
this state, allowing these commands to complete much quicker.

When installed, the fsmonitor extension will automatically launch a
background watchman daemon for accessed Mercurial repositories. It
should "just work."

Would you like to enable fsmonitor (Yn)?  n
Enable logging of commands to help diagnose bugs and performance problems (Yn)  n
The firefoxtree extension makes interacting with the multiple Firefox
repositories easier:

* Aliases for common trees are pre-defined. e.g. `hg pull central`
* Pulling from known Firefox trees will create "remote refs" appearing as
  tags. e.g. pulling from fx-team will produce a "fx-team" tag.
* The `hg fxheads` command will list the heads of all pulled Firefox repos
  for easy reference.
* `hg push` will limit itself to pushing a single head when pushing to
  Firefox repos.
* A pre-push hook will prevent you from pushing multiple heads to known
  Firefox repos. This acts quicker than a server-side hook.

The firefoxtree extension is *strongly* recommended if you:

a) aggregate multiple Firefox repositories into a single local repo
b) perform head/bookmark-based development (as opposed to mq)

(Relevant config option: extensions.firefoxtree)

Would you like to activate firefoxtree (Yn)?  n
It is common to want a quick view of changesets that are in progress.

The ``hg wip`` command provides such a view.

Example Usage:

  $ hg wip
  @  5887 armenzg tip @ Bug 1313661 - Bump pushlog_client to 0.6.0. r=me
  : o  5885 glob mozreview: Improve the error message when pushing to a submitted/discarded review request (bug 1240725) r?smacleod
  : o  5884 glob hgext: Support line breaks in hgrb error messages (bug 1240725) r?gps
  :/
  o  5883 mars mozreview: add py.test and demonstration tests to mozreview (bug 1312875) r=smacleod
  : o  5881 glob autoland: log mercurial commands to autoland.log (bug 1313300) r?smacleod
  :/
  o  5250 gps ansible/docker-hg-web: set USER variable in httpd process
  |
  ~

(Not shown are the colors that help denote the state each changeset
is in.)

(Relevant config options: alias.wip, revsetalias.wip, templates.wip)

Would you like to install the `hg wip` alias (Yn)?  n

Your system should be ready to build Firefox for Desktop Artifact Mode!


Paste the lines between the chevrons (>>> and <<<) into your mozconfig file:

<<<
# Automatically download and use compiled C++ components:
ac_add_options --enable-artifact-builds
>>>
```
* Create a file named "mozconfig" inside of the gecko folder and inside the "mozconfig" file, paste the following:
```
ac_add_options --enable-debug-js-modules
ac_add_options --enable-artifact-builds

mk_add_options AUTOCLOBBER=1
mk_add_options MOZ_OBJDIR=./objdir-frontend
```
* Now, let's build Firefox:
```
./mach build
```
* After completing your build of Firefox. To run the local Firefox you built:
```
./mach run
```

