# NCTracer Web Documentation

This repo contains the proposed documentation tool for NCTWeb
Sphinx is what is called a documentation generator. This means that it takes a bunch of source files in plain text, and generates a bunch of other awesome things, mainly HTML. For our use case you can think of it as a program that takes in plain text files in reStructuredText format, and outputs HTML.

reST, Markdwon -> Sphinx -> HTML

So as a user of Sphinx, your main job will be writing these text files. This means that you should be minimally familiar with reStructuredText as a language. It’s similar to Markdown in a lot of ways, if you are already familiar with Markdown.

This repo contains a very simple example of how to set up and use Sphinx to generate Python documentation.

## Basic instructions

0. Install Python on your machine. Depending on your operating system, the instructions may vary.
1. Create and activate a virtual environment for your project. You can use conda for this:

   ```bash
   $ conda env create -f environment.yml
   $ conda activate minimal-sphinx
   ```

   Another option is to use virtualenv. Create a

   ```bash
   $ pip install virtualenv  # not needed if you have used virtualenv before
   $ virtualenv venv
   $ source venv/bin/activate
   ```

   (A subtle difference between both methods is that the virtualenv environment you created lives in the folder where it was created, whereas the conda environment will work in whatever folder you are on.)

2. Install sphinx

	If you have used the conda instructions above, you all all set. If you prefer using pip, you can do

	```bash
	$ pip install -r requirements.txt
	```

	This will install all required packages, including `sphinx` itself and the `furo` Sphinx theme.

3. Initiate sphinx

	If you are starting a project from scratch, you can do

	```bash
	$ sphinx-quickstart
	```

	to get an initial file structure and configuration for your documentation. You can set default values for most of the answers, but you should fill your name and the project's name. The final message should say:

	> Finished: An initial directory structure has been created.

	> You should now populate your master file /home/abatra/projects/nctracerdocumentation/index.rst and create other documentation source files. Use the Makefile to build the docs, like so:
    >     make builder
    > where "builder" is one of the supported builders, e.g. html, latex or linkcheck

	This means that once you have your documentation, you can choose in which format to build it to. For example, to build an html version of the docs, you can navigate to the `docs/` folder and use

	```bash
	$ make html
	```

	However, if you run this command now, you may see some WARNINGS - this is expected as we haven't actually created any documents to build.

	(For this repo, this initial configuration has already been run, so you don't need to do it again.)

4. You can check the current folder for a bunch of automatically created files and folders. If you open `_build/html/index.html`, you can see an autogenerated starting point for your documentation. So now, let's create some documents!

## Documenting a Python module

We'll create a very simple Python module to test our setup. It's a starter Pokédex, containing just the three starter Pokémon from the Kanto region.

### The `index.rst` file and the reStructuredText format

After running `sphinx-quickstart`, an `index.rst` file is created that serves as the entry-point to your module documentation (unless you want to customize this - we'll see how you can do that below). It will likely contain only a basic structure in a [reStructuredText format](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html). reStructuredText, or reST, is a markup syntax that allows you to autogenerate documentation, including inline markup, custom content and powerful linking and referencing features.

The standard reST inline markup consists of
- one asterisk: `*text*` for emphasis (italics),
- two asterisks: `**text**` for strong emphasis (boldface), and
- backquotes: `` `text` `` for code samples.

In addition, reST also implements [directives](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html#directives), which are blocks of explicit markup which can have arguments, options and content. We'll see some examples of that in our module documentation.

For now, you can see some directives in the `index.rst` file: `toctree` is a reStructuredText directive; `maxdepth` and `caption` are options for this directive, and their values are `2` and `Contents:`. In addition, you can see the `:ref:` *role* in this file. Sphinx implements [interpreted text roles](https://www.sphinx-doc.org/en/master/usage/restructuredtext/roles.html) to insert semantic markup into documents. For example, ``:ref:`search` `` implements the role `ref` with the content `search` - in this particular case, we are cross-referencing a location, in this case creating a link to the document with label `search`.

We'll see concrete examples of this in the NumPy documentation, but you can check out a [nice summary of reST syntax](https://sphinx-tutorial.readthedocs.io/step-1/) and a longer [reST primer](https://www.sphinx-doc.org/en/master/usage/restructuredtext/index.html).

### The `conf.py` file

In order to properly autogenerate our documentation, we must edit a configuration file called `conf.py`, which is created by `sphinx-quickstart` with default values. You should see this in the root folder of your project.

For now, the first thing to customize is the project name and author, if you have to. After that, we'll add `'venv'` to the `exclude_patterns` list, if you're using venv to manage your developer environment. With this basic configuration we create the documentation with `make html`.

### Generating your module documentation

Right now, there's nothing in your rendered documentation. Let's fix that.

When documenting Python code, it is common to write *docstrings*. Sphinx supports the inclusion of docstrings from your modules with an extension called [autodoc](https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html#module-sphinx.ext.autodoc).

In order to use autodoc, you need to activate it in `conf.py` by writing

```
extensions = ['sphinx.ext.autodoc']
```

You can then document whole classes or even modules automatically, using member options for the auto directives, like

```
.. automodule:: io
   :members:
```

Note that autodoc needs to import your modules in order to extract the docstrings. Therefore, you must add the appropriate path to `sys.path` in your `conf.py`.

### doctests

Another extension that is helpful is [sphinx.ext.doctest](https://www.sphinx-doc.org/en/master/usage/extensions/doctest.html). This allows you to add tests *to your docstrings*. All you need to do is add `'sphinx.ext.doctest'` to your `extensions` variable in `conf.py`. After this, you can add doctests as examples in your docstrings. You can then run

```
$ make doctest
```

to see the results.

### Adding custom pages

Because we want to add more to our documentation than just the docstrings, let's add some custom pages to our project.

1. Move the API documentation to a separate page.

   First, let's create a new `apidocs.rst` file and move the `automodule` directive to this new file. After this, we'll add `apidocs` to our ToC Tree - our Table of Contents in the main `index.rst` file.

2. Add a Quickstart page to our documentation

   We'll write the *Quickstart* page separately.

### Intersphinx

Finally, we'll check the `sphinx.ext.intersphinx` extension. Adding this to our extensions list, we can refer to other project's documentation labels easily by using their *intersphinx mapping*. For an example, check the **Base Stats** section in the Quickstart document of this package.

## References

- [Sphinx tutorial](https://www.sphinx-doc.org/en/master/tutorial/index.html)
- [Sphinx documentation](https://www.sphinx-doc.org/en/master/)
- [Matplotlib Sphinx tutorial](https://matplotlib.org/sampledoc/)
- [Sphinx tutorial](https://sphinx-tutorial.readthedocs.io/) by Eric Holscher
- [A beginner's guide to writing documentation](https://www.writethedocs.org/guide/writing/beginners-guide-to-docs/), also by Eric Holscher



# PageCrypt Documentation

## PageCrypt has a very simple Command Line Interface with Python ##
This tool securely password-protects HTML files from the command line using just Python3.

### Usage ###
Just Install Python3 and Clone PageCrypt repository from here https://github.com/MaxLaumeister/PageCrypt 
Now Change current directory to PageCrypt/Python/
Run: `pip3 install -r requirements.txt` 
Run: `python3 encrypt.py filename [passphrase]`  

**Note:** filename is the entire filepath in case the html file is not in the /Python/ directory.

The first, mandatory parameter is the name of the file you want to encrypt.
It is also possible to give the passphrase as the second parameter. If there is
no passphrase provided at start, the script will ask for it while running.

### Dependencies ###
1) Python3 and Pip3
2) [pycryptodome](https://pypi.org/project/pycryptodome/) for AES. (pycrypto is [deprecated](https://github.com/pycrypto/pycrypto/issues/275))
3) Python version of [pbkdf2](https://pypi.org/project/pbkdf2/): `pip3 install pbkdf2`


### Demo 

Goto: https://mnbatra.github.io/nctracer-documentation/html/
Click on: “Introduction” section in the left sidebar.
The page is encrypted and paraphrase is “aman” 
If you refresh the page it will be locked again.

