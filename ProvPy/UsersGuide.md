# ProvPy User's Guide

This page describes how to deploy ProvPy and its prerequisites.

---

## Operating systems

The instructions have been written with reference to the 64-bit [Ubuntu](http://www.ubuntu.com/) 14.04.1 operating system.

Other operating systems, or versions of these, may differ in how packages are installed, the versions of these packages available from package managers etc. Consult the relevant documentation for your operating system and the products concerned.

Some prerequisites require you to have sudo access to install and configure software (or a local system administrator can do this for you):

    $ sudo su -

---

## Install Graphviz

dot is a program for drawing directed graphs. It is part of [Graphviz](http://www.graphviz.org/) open source graph visualization software. 

Install:

    $ sudo apt-get install graphviz
    $ dot -V
    dot - graphviz version 2.36.0 (20140111.2315)

---

## Install cURL

[cURL](http://curl.haxx.se/) is a very useful command line tool and library for interacting over HTTP(S).

Install:

    $ sudo apt-get install curl
    $ curl --version
    curl 7.35.0 (x86_64-pc-linux-gnu) libcurl/7.35.0 OpenSSL/1.0.1f zlib/1.2.8 libidn/1.28 librtmp/2.3

---

## Install zlib development library

[zlib](http://www.zlib.net/) is a compression library.

Install:

    $ sudo apt-get install zlib1g-dev

---

## Install libxslt development libraries

[libxslt](http://xmlsoft.org/XSLT/) is an XSLT library. It is part of the [libxml2](http://xmlsoft.org/) XML parser and toolkit.

Install:

    $ sudo apt-get install libxslt1-dev

---

## Install Python and its tools system-wide

If you have sudo access then you can install Python system-wide.

Alternatively, if you do not have sudo access, or want to install Python in your home directory, see the following section on pyenv.

### Install Python

Install Python 2:

    $ sudo apt-get install python
    $ python --version
    Python 2.7.6

Alternatively, install Python 3:

    $ sudo apt-get install python3
    $ python3 --version
    Python 3.4.0

### Install pip

[pip](https://pypi.python.org/pypi/pip) is the Python package manager.

Install for Python 2:

    $ sudo apt-get install python-pip
    $ pip -V
    pip 1.5.4 from /usr/lib/python2.7/dist-packages (python 2.7)

Alternatively, install for Python 3:

    $ sudo apt-get install python3-pip
    $ pip3 -V
    pip 1.5.4 from /usr/lib/python3/dist-packages (python 3.4)

### Install setuptools

[setuptools](https://pypi.python.org/pypi/setuptools) is a package that supports the download, build, install and upgrade of Python packages.

Install for Python 2:

    $ sudo apt-get install python-setuptools

Alternatively, install for Python 3:

    $ sudo apt-get install python3-setuptools

### Install virtualenvwrapper

[virtualenv](https://pypi.python.org/pypi/virtualenv) is a tool that creates isolated Python environments with all the executables and packages that that specific environment needs. [virtualenvwrapper](https://virtualenvwrapper.readthedocs.org/en/latest/) extends virtualenv with wrappers to make creating and managing virtual environments easier.

Install for Python 2:

    $ sudo pip install virtualenvwrapper

Alternatively, install for Python 3:

    $ sudo pip3 install virtualenvwrapper

### Set up virtual environments

Create environments directory. For example:

    $ mkdir ~/Envs

Edit ~/.bash_profile and add the lines:

    export WORKON_HOME=~/Envs
    source /usr/local/bin/virtualenvwrapper.sh

Set environment:

    $ source ~/bash_profile

---

## Install Python and its tools locally

[pyenv](https://github.com/yyuu/pyenv) allows you to deploy multiple versions of Python within your own directory, and to switch between these versions.

### Install pyenv dependencies

pyenv dependencies has a number of dependencies, which need to be installed by someone with sudo access.

Install:

    $ sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm

### Install pyenv

Run:

    $ curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash

Edit ~/.bash_profile and add the lines:
    
    export PATH="$HOME/.pyenv/bin:$PATH"
    eval "$(pyenv init -)"
    eval "$(pyenv virtualenv-init -)"

Set environment:

    $ source ~/.bash_profile

### Install Python

See Python versions available:
    
    $ pyenv update
    $ pyenv install -l

Install desired versions. For example:

    $ pyenv install 2.7.6

Or:

    $ pyenv install 3.4.0

**Troubleshooting: 'WARNING: The Python ... extension was not compiled.'**

If you get warnings like:

    WARNING: The Python readline extension was not compiled. Missing the GNU readline lib?
    WARNING: The Python bz2 extension was not compiled. Missing the bzip2 lib?
    WARNING: The Python sqlite3 extension was not compiled. Missing the SQLite3 lib?
    ERROR: The Python ssl extension was not compiled. Missing the OpenSSL lib?
    ERROR: The Python ssl extension was not compiled. Missing the OpenSSL lib?

Then install the pyenv dependencies. See pyenv's [common build problems](https://github.com/yyuu/pyenv/wiki/Common-build-problems)

---

## Install ProvPy

### Create a virtual environment (optional)

If using virtual environments, then create a virtual environment for ProvPy. For example:

    $ mkvirtualenv prov

### Select your pyenv Python version (optional)

If using pyenv, then select the Python version into whose packages you want to install ProvPy. For example:

    $ pyenv local 2.7.6

### Install ProvPy
 
    $ pip install prov
    $ pip list | grep prov
    prov (1.3.1)
    $ pip show prov
    ---
    Name: prov
    Version: 1.3.1
    Location: /usr/local/lib/python2.7/dist-packages
    Requires: six, python-dateutil, lxml, networkx

Alternatively, if you have installed setuptools, you can do:

    $ easy_install prov

---

## Run prov-convert

prov-convert is a script that converts PROV documents. For usage information, run:

    $ prov-convert --help

This only runs under Python 2.7.

For example, create a file, `example.json`:

    {"prefix": {"default": "http://example.org/0/", "ex2": "http://example.org/2/", "ex1": "http://example.org/1/"}, "bundle": {"e001": {"prefix": {"default": "http://example.org/2/"}, "entity": {"e001": {}}}}, "entity": {"e001": {}}}

Run:

    $ prov-convert -f provn example.json example.provn
    $ cat example.provn 
    document
      default <http://example.org/0/>
      prefix ex2 <http://example.org/2/>
      prefix ex1 <http://example.org/1/>
  
      entity(e001)
      bundle e001
        default <http://example.org/2/>
    
        entity(e001)
      endBundle
    endDocument

    $ prov-convert -f xml example.json example.xml
    $ cat example.xml 
    <?xml version='1.0' encoding='UTF-8'?>
    <prov:document xmlns:prov="http://www.w3.org/ns/prov#" xmlns:ex2="http://example.org/2/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:ex1="http://example.org/1/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://example.org/0/">
      <prov:entity prov:id="e001"/>
      <prov:bundleContent prov:id="e001">
        <prov:entity prov:id="e001"/>
      </prov:bundleContent>
    </prov:document>
    
    $ prov-convert -f pdf example.json example.pdf
    $ prov-convert -f svg example.json example.svg
    $ prov-convert -f dot example.json example.dot

### Troubleshooting: 'No module named pydot'

If, when specifying `.pdf`, `.svg`, or `.dot` as an output format you see:

    $ prov-convert -f pdf example.json example.pdf
    prov-convert: No module named pydot
                  for help use --help
    
    $ prov-convert -f svg example.json example.svg
    prov-convert: No module named pydot
                  for help use --help(

### Troubleshooting: 'GraphViz's executables not found'

If, when specifying `.pdf`, `.svg`, or `.dot` as an output format you see:

    $ prov-convert -f png example.json example.png
    prov-convert: GraphViz's executables not found
              for help use --help
    $ prov-convert -f dot example.json example.dot
    ...as above...

Then install Graphviz.

### Troubleshooing: 'prov-convert: command not found'

If, when using pyenv, you get:

    $ prov-convert --help
    prov-convert: command not found

then try:


    .pyenv/versions/2.7.6/bin/prov-convert --help

Remember to replace 2.7.6 with your version of Python.

---

## Run prov-compare

prov-compare is a script that compares PROV documents to see if they are semantically equivalent. For usage information, run:

    $ prov-compare --help

This only runs under Python 2.7.

### Troubleshooing: 'prov-compare: command not found'

If, when using pyenv, you get:

    $ prov-compare --help
    prov-compare: command not found

then try:


    .pyenv/versions/2.7.6/bin/prov-compare --help

Remember to replace 2.7.6 with your version of Python.

---

## Write examples using prov

Create a file, `simple-example.py`:

    import prov.model as prov
    import datetime
    
    document = prov.ProvDocument()
    
    document.set_default_namespace('http://anotherexample.org/')
    document.add_namespace('ex', 'http://example.org/')
    
    e2 = document.entity('e2', (
        (prov.PROV_TYPE, "File"),
        ('ex:path', "/shared/crime.txt"),
        ('ex:creator', "Alice"),
        ('ex:content', "There was a lot of crime in London last month"),
    ))
    
    a1 = document.activity('a1', datetime.datetime.now(), None, {prov.PROV_TYPE: "edit"})
    # References can be qnames or ProvRecord objects themselves
    document.wasGeneratedBy(e2, a1, None, {'ex:fct': "save"})
    document.wasAssociatedWith('a1', 'ag2', None, None, {prov.PROV_ROLE: "author"})
    document.agent('ag2', {prov.PROV_TYPE: 'prov:Person', 'ex:name': "Bob"})
    
    print(document.get_provn())

Run:

    $ python simple-example.py
    document
      default <http://anotherexample.org/>
      prefix ex <http://example.org/>
      
      entity(e2, [prov:type="File", ex:content="There was a lot of crime in London last month", ex:creator="Alice", ex:path="/shared/crime.txt"])
      activity(a1, 2015-05-07T07:45:28.698831, -, [prov:type="edit"])
      wasGeneratedBy(e2, a1, -)
      wasAssociatedWith(a1, ag2, -, [prov:role="author"])
      agent(ag2, [prov:type="prov:Person", ex:name="Bob"])
    endDocument

Create a file, `bundle-example.py`:
    
    import prov.model as prov
    
    document = prov.ProvDocument()
    
    document.set_default_namespace('http://example.org/0/')
    document.add_namespace('ex1', 'http://example.org/1/')
    document.add_namespace('ex2', 'http://example.org/2/')
    
    document.entity('e001')
    
    bundle = document.bundle('e001')
    bundle.set_default_namespace('http://example.org/2/')
    bundle.entity('e001')
    
    print (document.get_provn())
    print (document.serialize())

Run:

    $ python bundle-example.py 
    document
      default <http://example.org/0/>
      prefix ex2 <http://example.org/2/>
      prefix ex1 <http://example.org/1/>
      
      entity(e001)
      bundle e001
        default <http://example.org/2/>
        
        entity(e001)
      endBundle
    endDocument
    {"prefix": {"default": "http://example.org/0/", "ex2": "http://example.org/2/", "ex1": "http://example.org/1/"}, "bundle": {"e001": {"prefix": {"default": "http://example.org/2/"}, "entity": {"e001": {}}}}, "entity": {"e001": {}}}
    
---

## Examples

See [prov/tests/examples.py](https://github.com/trungdong/prov/blob/master/prov/tests/examples.py).

---

## Troubleshooting: 'No module named pydot'

If, when specifying `pdf`, `svg`, `dot` or another graphical format you see, when running your Python script:

    ImportError: No module named pydot

Then, if using Python 2, install [pydot](https://pypi.python.org/pypi/pydot):

    $ pip install pydot

If using Python 3, install a [Python 3-compatible](https://bitbucket.org/prologic/pydot/commits/ac76697320d6fbbb211ccfa4853bff041f3a348c) version of pydot:

    $ pip install https://bitbucket.org/prologic/pydot/get/ac76697320d6.zip

---

## Troubleshooting: 'Couldn't import dot_parser, loading of dot files will not be possible.'

If you get this error when running code that uses ProvPy:

    Couldn't import dot_parser, loading of dot files will not be possible.

then please ignore it. Dot files are not loaded by ProvPy components.

---


