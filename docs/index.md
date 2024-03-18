# MkDocs

## Index

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

## Prerequisites

You'll need python & pip installed  
If you want seemless deployments to GitHub pages, you'll also need a GitHub account.

## Installation

First things first, create a new public repository in your GitHub space. I will call mine `homelab-docs`.  
Once that is done, you can clone the repo on your local server:

```
rademakr@prismpi[~]$ git clone git@github.com:rademakr/homelab-docs.git
```

That will create a new homelab-docs directory that is git and GitHub related.  
Now we can install mkdocs.


``` { .sh .no-copy linenums="1" hl_lines="1 2 5 6" }
rademakr@prismpi[~/homelab-docs]$ python -m venv venv
rademakr@prismpi[~/homelab-docs]$ source venv/bin/activate
(venv) rademakr@prismpi[~/homelab-docs]$ pip --version
pip 23.0.1 from /home/rademakr/homelab-docs/venv/lib/python3.11/site-packages/pip (python 3.11)
(venv) rademakr@prismpi[~/homelab-docs]$ pip install mkdocs-material
(venv) rademakr@prismpi[~/homelab-docs]$ pip install "mkdocs-material[imaging]"

```
This will install mkdocs-material.

!!! note
    All these lines have their importance. 
    On line 1, we create a python virtual environment. Virtual environments are important to isolate the project (in this case mkdocs) from the rest of the system.
    Line 2 shows how to activate the virtual environment which will be the default Python interpreter for the duration of the shell session.  
    You need to run the line 2 command `source venv/bin/activate` from the homlab-docs directory each time you work on the mkdocs project and want to serve pages.  
    Lines 5 and 6 install Python modules required for mkdocs-material (line 5) and those for the 'social' plugin (line 6).  
    You can serve pages by running `mkdocs serve`.

We'll create our files with the following command:

`(venv) rademakr@prismpi[~/homelab-docs]$ mkdocs new .`

Which will create two files :

```
INFO    -  Writing config file: ./mkdocs.yml
INFO    -  Writing initial docs: ./docs/index.md
```

The `mkdocs.yml` file is where you'll set all the settings for the website, while the `ìndex.md` in the `docs` direcory is a markdown page
that will be the first tab of the homepage. We'll be able to remove that file later, or update it with the text you want...

The idea is to add one .md file per project. Thus I have this file named `mkdocs.md` for the instructions on setting up mkdocs.
Might also want to create a "fixed" index, with directories per project in order to keep the upper hand on the sorting of pages (I believe by default, mkdocs sorts alphabetically).

I will add one for Ansible, and a few to describe specific middleware configuration.

## GitHub integration with GitHub-Actions

Create the `.github` directory and `workflows` subdirectory. In the `workflows` directory create a file `ci.yml`


```
mkdir -p .github/workflows
vi .github/workflows/ci.yml
```

and paste the following:

```yaml
name: ci
on:
  push:
    branches:
      - master
      - main
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: 3.x
      - uses: actions/cache@v2
        with:
          key: ${{ github.ref }}
          path: .cache
      - run: pip install mkdocs-material
      - run: pip install pillow cairosvg
      - run: mkdocs gh-deploy --force
```

## TODO

git initialization (1)
{ .annotate }

1. need to explain how to setup the GitHub repo and init the git env

