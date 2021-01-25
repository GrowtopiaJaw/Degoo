# Table of contents
* [Degoo CLI Util(s)](#degoo-cli-utils)
  * [What currently works](#what-currently-works)
  * [What doesn't work](#what-doesnt-work)
* [Using these tools](#using-these-tools)
  * [Behind the scene](#behind-the-scene)
* [Installation](#installation)
  * [Prerequisite](#prerequisite)
* [Usage](#usage)
  * [degoo cd](#degoo-cd)
  * [degoo get](#degoo-get)
  * [degoo ll](#degoo-ll)
  * [degoo login](#degoo-login)
  * [degoo ls](#degoo-ls)
  * [degoo mkdir](#degoo-mkdir)
  * [degoo path](#degoo-path)
  * [degoo props](#degoo-props)
  * [degoo put](#degoo-put)
  * [degoo pwd](#degoo-pwd)
  * [degoo rm](#degoo-rm)
  * [degoo test](#degoo-test)
  * [degoo tree](#degoo-tree)
  * [degoo user](#degoo-user)
* [Additional project information](#additional-project-information)


## Degoo CLI Util(s)

Degoo are a cloud storage provider based in Sweden, who provide fairly goo phone apps and web interface along with affordable plans, of up 10 TB storage.

[https://degoo.com/](https://degoo.com/)

Unfortunately the apps (phone and web) are pitched to a very particular demography of user, and are rounded with great tools for storing photos, videos, music and documents in the cloud.

This makes it very difficult to use the cloud storage for flexible backup of data.

Here's a fairly impartial review you might find useful:

[Degoo Review 2020 - This Is Why You Shouldn't Use It](https://cloudstorageinfo.org/degoo-review)

Support seems hit and miss. They are a small company:

[https://degoo.com/about](https://degoo.com/about)

And have (only) two people on Customer Support so if they have [15 million users](https://www.techradar.com/news/the-best-cloud-storage#4-degoo), then  clearly they'd struggle to deliver customer support well. It's called outgrowing your boots.

They have also chopped and churned, originally using P2P storage then moving away form that after a load of [poor reviews]((https://www.trustpilot.com/review/degoo.com)). They had a Windows desktop client, but no more. They are still, it seems clearly trying to find their niche in this market and establish a service model that secures a lasting future.

My interest is in keeping server data backed-up in the cloud.

And so, by studying their web app (which in written in Angular JS and managed with Webpack, communicating with a backend over a [graphQL]((https://graphql.org/)) interface) I've written a simple CLI (command line interface) to the cloud storage.

It is written in Python and being developed under Linux. Being Python it's very likely highly portable, but there may be some small issues running on other systems. The only issues I can think of currently are:

* It tries to use os.sep intelligently to give you a natural feel if say you're using Windows where it's `\` rather than Linux of MacOS where it's `/`. But that's untested so far on Windows.

* It uses [python-magic](https://pypi.org/project/python-magic/) to determine file types (needed for upload, as the API seems to demand this metadata). That may have some system dependencies.

* It uses a custom patched version of [python-wget](https://github.com/bernd-wechner/python3-wget) as the one that pip provides is lacking some cucial features and the package seems sadly unmaintained and dead to the world. This patched version stands as an op Pull Request on the upstream with no action.

#### What currently works

A work in progress, it's not complete but at present it can reliably:

* log you in (if you provide valid credentials)

* list files and folders on the clour drive (ls, tree)

* navigate the cloud drive (cd, pwd)

* manipulate the cloud drive (mkdir, rm)

* download files from the cloud drive (get)

* upload files to the cloud drive (put)

#### What doesn't work

Not implemented yet:

* [Top Secret Cloud Storage](https://help.degoo.com/support/solutions/articles/77000065516-top-secret-zero-knowledge-storage)

  * Degoo provide a good security focussed solution with their Top Secret vault, that they claim is 100% NSA proof. Only available on their phone app for now, not the web app. Will take some effort to analyse the cient-server interactions to provide CLI support.

* Device creation

  * Top level directories on your Degoo cloud drive are reserved for devices. Different licenses provide different numbers of devices. Currently you can delete a device but there is no facility for adding one again, or if you're an Ulitmate license holder adding new devices (which should be possible, but the web interface provides no such facility).

### Using these tools

This is a work in progress (WIP) still and may or may not work well. Works for me ;-). But here are some quick tips if you're wanting to try it.

* The core of it is all implemented in two files:
    * `degoo/__init__.py` which provides all the basic functions needed
    * `commands.py` which implements a command line tool (that is sesnitive to its name)

* To build the command line tools there:
    * `build.py` - which just creates a pile of links to `commands.py` named as a command line tools. That's a dirty trick of sorts I used to give me a pile of CLI commands to work with so I can write bash scripts etc.

* There's no system installer yet, it's all working in the lcoal dir as I work on it. I haven't yet put this to use as a serious backup strategy and am working on some areas to get there (slowly, when time permits)

* If you want to debug, personally I can't recommend Eclipse+PyDev more highly, that's what I use. PyCharm is popular but freemium, might be easier to get started with.

#### Behind the scene

* If you're wanting to look at how it's been engineered:
    * Surf to degoo.com in Firefox or Chrome
    * Open the Developer tools (F12)
    * Click the Network tab
    * Log in to degoo and watch the traffic. 
    * You can save all that into text files and then start diagnosing. 

### Installation

This project has been tested on Ubuntu and it might work on Windows and macOS but no guarantee can be made.

#### Prerequisite

* Python 3
* Python pip

Before installing the required Python modules, make sure that you have cloned or downloaded the project and is in the project's root directory

Linux, macOS and Windows

```
$ python3 -m pip install -r requirements.txt
```

### Usage

First, you have to compile the project in order to use the tools

```
$ python3 build.py
```

#### degoo cd

A command to change into (open) subdirectories (folder) of your Degoo cloud storage

```
$ python3 degoo_cd

usage: degoo_cd [-h] [-v] [-V] folder

USAGE:

positional arguments:
  folder         The folder/path to makre current.

optional arguments:
  -h, --help     show this help message and exit
  -v, --verbose  set verbosity level [default: 0]
  -V, --version  show program's version number and exit
```

#### degoo get

A command to download specified file or directories (folder) from your Degoo cloud storage

```
$ python3 degoo_get

usage: degoo_get [-h] [-v] [-V] [-d] [-f] [-s] remote [local]

USAGE:

positional arguments:
  remote           The file/folder/path to get
  local            The directory to put it in (current working directory if
                   not specified)

optional arguments:
  -h, --help       show this help message and exit
  -v, --verbose    set verbosity level [default: 0]
  -V, --version    show program's version number and exit
  -d, --dryrun     Show what would be uploaded but don't upload it.
  -f, --force      Force downloads, else only if local file missing.
  -s, --scheduled  Download only when the configured schedule allows.
```

#### degoo ll

TBD

```
$ python3 degoo_ll

usage: degoo_ll [-h] [-v] [-V] [-l] [-R] [folder]

USAGE:

positional arguments:
  folder           The folder/path to list

optional arguments:
  -h, --help       show this help message and exit
  -v, --verbose    set verbosity level [default: 0]
  -V, --version    show program's version number and exit
  -l, --long
  -R, --recursive
```

#### degoo login

A command to log into your Degoo cloud storage account

```
$ python3 degoo_login

Successful login message:     Successfuly logged in.
Failed login message:         Login failed.
No login information message: No login credentials available. Please add account details to credentials.json
```

#### degoo ls

A command to display contents of your Degoo device backup folder

```
$ python3 degoo_ls

usage: degoo_ls [-h] [-v] [-V] [-l] [-R] [folder]

USAGE:

positional arguments:
  folder           The folder/path to list

optional arguments:
  -h, --help       show this help message and exit
  -v, --verbose    set verbosity level [default: 0]
  -V, --version    show program's version number and exit
  -l, --long
  -R, --recursive
```

#### degoo mkdir

A command to create new directories (folder) within your Degoo device backup

```
$ python3 degoo_mkdir

usage: degoo_mkdir [-h] [-v] [-V] folder

USAGE:

positional arguments:
  folder         The folder/path to list

optional arguments:
  -h, --help     show this help message and exit
  -v, --verbose  set verbosity level [default: 0]
  -V, --version  show program's version number and exit
```

#### degoo path

A command to display the ID of a particular directory (folder) or file

```
$ python3 degoo_path

usage: degoo_path [-h] [-v] [-V] path

USAGE:

positional arguments:
  path           The path to test.

optional arguments:
  -h, --help     show this help message and exit
  -v, --verbose  set verbosity level [default: 0]
  -V, --version  show program's version number and exit
```

#### degoo props

A command to display the properties of a particular directory (folder) or file

```
$ python3 degoo_props

usage: degoo_props [-h] [-v] [-V] [-R] [-b] path

USAGE:

positional arguments:
  path             The name, path, or ID of degoo item to return properties of (can be a device, folder, file).

optional arguments:
  -h, --help       show this help message and exit
  -v, --verbose    set verbosity level [default: 0]
  -V, --version    show program's version number and exit
  -R, --recursive
  -b, --brief
```

#### degoo put

A command to upload specified file or directories (folder) to your Degoo cloud storage

```
$ python3 degoo_put

usage: degoo_put [-h] [-v] [-V] [-d] [-f] [-s] local [remote]

USAGE:

positional arguments:
  local            The file/folder/path to put
  remote           The remote folder to put it in

optional arguments:
  -h, --help       show this help message and exit
  -v, --verbose    set verbosity level [default: 0]
  -V, --version    show program's version number and exit
  -d, --dryrun     Show what would be uploaded but don't upload it.
  -f, --force      Force uploads, else only upload if changed.
  -s, --scheduled  Upload only when the configured schedule allows.
```

#### degoo pwd

A command to display which directory (folder) you are currently in you Degoo cloud storage

```
$ python3 degoo_pwd

Working Directory is <folder>
```

#### degoo rm

A command to move file or directories (folder) into a Degoo device backup's Recycle Bin

```
$ python3 degoo_rm

usage: degoo_rm [-h] [-v] [-V] file

USAGE:

positional arguments:
  file           The file/folder/path to remove

optional arguments:
  -h, --help     show this help message and exit
  -v, --verbose  set verbosity level [default: 0]
  -V, --version  show program's version number and exit
```

#### degoo test

A command to test file uploads to a user's Degoo cloud storage

Note: This command was specifically created by the project's creator to test file uploads to his personal Degoo cloud storage. This was never meant to be used by end users.

```
$ python3 degoo_test

degoo_test: [Errno 2] No such file or directory: '/home/bernd/workspace/Degoo/test_data/Image/Image 2.jpg'
            for help use --help
```

#### degoo tree

A command to display contents of directories (folder) in a tree-like format

```
$ python3 degoo_tree

usage: degoo_tree [-h] [-v] [-V] [-t] [folder]

USAGE:

positional arguments:
  folder         The folder to put_file it in

optional arguments:
  -h, --help     show this help message and exit
  -v, --verbose  set verbosity level [default: 0]
  -V, --version  show program's version number and exit
  -t, --times    Show timestamps
```

#### degoo user

A command to display the information of the Degoo cloud storage account that is currently logged into

```
$ python3 degoo_user

Logged in user:
	Name: <name>
	Email: <email>
	Phone: <phone number>
	AvatarURL: <profile picture url>
	AccountType: <account subscription type>
	UsedQuota: <used storage space>
	TotalQuota: <available storage space>
 ```
 
### Additional project information
 
This project was originally created by [Bernd Wechner](https://github.com/bernd-wechner) and I forked this project to make proper changes so that it would work properly with my Degoo cloud storage account. All credits should go to [Bernd Wechner](https://github.com/bernd-wechner) as I would never be able to download all my files from my Degoo account and would have to painstakingly download every single file ever stored manually. Please star his project if you find this project useful to you.


```
Created by Bernd Wechner on 2020-06-03.
Copyright 2020-2021. All rights reserved.

Licensed under The Hippocratic License 2.1
https://firstdonoharm.dev/

Distributed on an "AS IS" basis without warranties
or conditions of any kind, either express or implied.
```
