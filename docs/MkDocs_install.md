# mkdocs

### Prerequisites

## Installation

You'll need python & pip installed


``` bash
rademakr@prismpi[~/homelab-docs]$ python -m venv venv
rademakr@prismpi[~/homelab-docs]$ source venv/bin/activate
(venv) rademakr@prismpi[~/homelab-docs]$ pip --version
pip 23.0.1 from /home/rademakr/homelab-docs/venv/lib/python3.11/site-packages/pip (python 3.11)
(venv) rademakr@prismpi[~/homelab-docs]$ pip install mkdocs-material

```
This will install mkdocs-material.

We'll create our website with the following command:

`(venv) rademakr@prismpi[~/homelab-docs]$ mkdocs new .`

Which will create two files :

```
INFO    -  Writing config file: ./mkdocs.yml
INFO    -  Writing initial docs: ./docs/index.md
```

The `mkdocs.yml` file is where you'll set all the settings for the website, while the `ìndex.md` in the `docs` direcory is a markdown page
that will be the first tab of the homepage. We'll be able to remove that file later, or update it with the text you want...

The idea is to add one .md file per project. Thus I have this file named `mkdocs.md` for the instructions on setting up mkdocs.

I will add one for Ansible, and a few to describe specific middleware configuration.

## GitHub integration with GitHub-Actions

Create the `.github` directory and `workflows` subdirectory. In the `workflows` directory create a file `ci.yml`


```
mkdir -p .github/workflows
vi .github/workflows/ci.yml
```

and paste the following:

```

```
