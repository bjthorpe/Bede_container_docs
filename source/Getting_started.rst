Getting started
===============

Initial setup
-------------
The bede_ml-toolkit was primarily built for use on 
the Grace-Hopper nodes of N8 HPC cluster Bede. You will
first need to register for an account. Instructions for 
this can be found in the `Bede Documentation.`_

You will also need to ensure you are logged into the 
Grace-Hopper partition using the `ghlogin command.`_

Alternatively if you wish to install the ML toolkit on 
you local machine you can just be aware some of the instructions 
will only apply to Bede (although I will try to make it clear when
this is the case).

.. _Bede Documentation.: https://bede-documentation.readthedocs.io/en/latest/usage/index.html#using-bede

.. _ghlogin command.: https://bede-documentation.readthedocs.io/en/latest/usage/index.html#connecting-to-the-ghlogin-node

Bede Installation instructions
------------------------------

Once you are logged into the Bede Grace-Hopper node you have two options you can either 
install bede_ml-toolkit for all users on the system using pipx or you can download the 
python source files and build it from there.

Using pip (recommended for most users):
----------------------------------------

To install bede_ml-toolit run the following commands:

.. code-block:: bash
    
    #create python virtual environment (recommended)
    python -m venv ~/.venv/bede_ml-toolkit
    source ~/.venv/bede_ml-toolkit/bin/activate
    # install bede_ml-toolkit
    pip install bede-ml-toolkit
    # if needed
    install_apptainer
    install_ml-toolkit

This will download and install all the necessary files to your system.
it will also install apptainer if needed and create a directory called
ML_Toolkit in your home directory.

This is used to house all the various config, definition files and container 
images used by ml-tookit. If you want to put this directory somewhere else 
on your system you can use the -p option along with where you wish to 
install it as:

.. code-block:: bash

    install_bede_ml-toolkit -p /path/to/install/to

Installing from source (recommended for developers/advanced users):
-------------------------------------------------------------------

You will first need to download the git repository code using:

.. code-block:: bash

    git clone https://github.com/bjthorpe/Bede_containersg
    git checkout dev
    cd Bede_containers

Next you will need to install some python packages using pip. We recommend using a 
virtual environment for this:

.. code-block:: bash

    python3 -m venv bede_ml-toolkit
    source bede_ml-toolkit/bin/activate
    pip install dacite pyyaml pytest
    pip install . 

Finally you will need to run the two install scripts:

.. code-block:: bash

    install_apptainer
    install_bede_ml-toolkit

The ``install_bede_ml-toolkit`` script also optionally has an option ``--dev`` which allows
you to use the Data directory of the git repo as ML_TOOLKIT_HOME, with git ignore setup to 
not track Images logs etc. This is useful if you are developing stuff your changes all 
tracked within git and you don't have to remember to copy code over to the ``~/ML_Toolkit`` 
directory for testing. 

It also pairs well with ``pip install -e .`` so you can change code in the repo and not have 
to re-run pip install.

Optionally you can now run the unit tests using:

.. code-block:: bash

    pytest

The ML_Toolkit directory
------------------------

The software we downloaded contains a number of different files 
and folders so I think it is worthwhile briefly covering the 
important files/folders and what each of them is used for.

All files used by bede_ml-tookit are located in the ``ML_Toolkit`` directory
which was created during installation and is located in the users 
home directory by default.

Note: if you ever need to know where this directory is located you can run:

.. code-block:: bash

    echo $ML_TOOLKIT_HOME

Getting our bearings
--------------------

The ML_toolkit directory contains the following sub-directories:

- **Container_Configs:** Contains all the .yaml config files used to configure the containers.
- **Definitions:** Contains a number of .def files used to build containers.
- **Scripts:** Contains various python scripts to demonstrate how to use pre-trained models from MatBench Discovery
- **Images:** Contains the container images (.sif) files used by Apptainer. These store all the data and files used by the container.
- **Models:** Checkpoint Files used for the various pre-trained models.
- **logs:** Log files containing more detailed output from the program, useful for debugging.

In order to use containers with the bede_ml-toolkit we need two things:

1. A container config (.yaml) file
2. A container definition
   
Both of these combined will tell bede_ml-toolkit and Apptainer what software, files 
and steps are needed in order to build our container. We will go into more detail 
about how these files are constructed  in later sections. 

The Container_Configs directory holds a bunch of example config files most of which
are for accessing for the various pre-trained models used for Machine learned atomic
potentials. See the section on :ref:`ML Atomic Potentials <Examples>`, for more details.

For this first example, we will be using Test_containers.yaml.
This defines two containers that consist of a tiny Linux installations that can be used for 
testing the installation of Apptainer. This will be used in the next section to test 
everything is installed correctly.

Related to this is the Definitions directory this contains various Apptainer definition 
(.def) files. These can be thought of as a blueprints that tells Apptainer how to 
setup the system and what software to install and are used by Apptainer to create the 
various containers.

With this out of the way we shall now run a small test container to check everything 
is working correctly.

Your first container
--------------------

To test everything is installed correctly we will now build and run a simple 
test container. This will consist of a tiny Linux installation which only has 
a program called `cowsay`_ installed. Cowsay is a small program that generates 
ASCII art pictures of a cow with a message.

.. _cowsay: https://cowsay.diamonds/

An Apptainer definition file to setup and run this container can be found under 
Definitions/cowsay.def. 

These can be quite involved and a full tutorial on Apptainer is beyond the scope 
of this document. However those interested can consult this `Tutorial`_ or the 
Apptainer `Documentation`_ for a full breakdown of how these files work. 

.. _Tutorial: https://deepwiki.com/apptainer/apptainer-userdocs/2.1-definition-files
.. _Documentation: https://apptainer.org/docs/user/main/definition_files.html

For our purposes however, we can build the container with the following 
command (note this is case sensitive):

.. code-block:: bash

    ml-toolkit build TestContainer

This will download and build the cowsay container from the definition file and create a 
container image TestContainer.sif in the Images directory. A container image contains 
all the files the container needs to run in a format Apptainer can understand.

Once this is complete we can run a command inside this container using:

.. code-block:: bash

    ml-toolkit run TestContainer COMMAND 

Where COMMAND is the Linux command we wish the container to run. In our case we will run 
a script (found in ML_Toolkit/Scripts directory) that runs the fortune command, to generate 
a random (possibly silly) quote then passes it into the cowsay command which will then 
display the message.

.. code-block:: bash

    ml-toolkit run TestContainer $ML_TOOLKIT_HOME/Scripts/speak_wisdom.sh

If all has gone to plan you should see similar output to the following:

.. code-block:: bash
    
    *********************************************************************
    ***************** Loading Model Config Files ************************
    *********************************************************************
    Reading config from file: Container_Configs/ollama.yaml
    Container_Configs/ollama.yaml OK
    Reading config from file: Container_Configs/test1.yaml
    Container_Configs/test1.yaml OK
    Reading config from file: Container_Configs/test2.yaml
    Container_Configs/test2.yaml OK
    *********************************************************************
    ***************** Running: TestContainer *********************
    *********************************************************************
    _________________________________________
    / You will be reincarnated as a toad; and \
    \ you will be much happier.               /
     -----------------------------------------
                \   ^__^
                \   (oo)\_______
                    (__)\       )\/\
                        ||----w |
                        ||     ||

You will also find a more detailed output in the logfile logs/log.log. 
This is overwritten each time you use the build or run command and 
notably does not contain the output of the container itself. In this 
case the Ascii art cow. However it does contain useful information including:

+ what files/folders the container is accessing
+ the variables used for each config bede_ml-toolkit has found
+ a summary of the underlying Apptainer commands the script is running 
+ Warnings about config issues that won't necessarily crash the container but may cause issues. 

All of which is useful for debugging.

Other useful commands:
----------------------

In addition to build and run commands there are several other useful commands which are worth knowing:

- list
- start
- stop
  
**List** is a useful command for checking what containers are available.

.. code-block:: bash

    ml-toolkit list

The full list however may mot be that useful as it's quite long. 
As such the --group option may be used to filter down based on the group tag.
For example  

.. code-block:: bash

    ml-toolkit list --group Test

Will list only list containers with the tag Test.

**Start** and **Stop** are also useful commands they allow you to run 
containers as background processes which is useful for certain types of software.

The **Start** command runs the command defined in the definition (.def) file 
under the section `%startscript`_. You can also list all currently running containers 
with:

.. code-block:: bash

    apptainer instance list.

.. _%startscript: https://apptainer.org/docs/user/main/definition_files.html#startscript

Getting Help:
-------------

If you need a quick refresher on which options exist and what they do this can be displayed 
using the -h or (--help) option as follows:

.. code-block:: bash

    ml-toolkit -h

    usage: run_container.py [-h] [--config_file CONFIG_FILE] [--debug] {run,build,load,list,start,stop} ...

    A CLI tool for easily running AI/ML containers on Bede.

    positional arguments:
    {run,build,load,list,start,stop}
                            Operation to perform.
        run                 Run command(s), with the Container
        build               Build the Container, exactly equivalent to load
        load                Build the Container, exactly equivalent to build
        list                List available containers
        start               Start Container as background process
        stop                Stop container that is running in the background

    options:
    -h, --help            show this help message and exit
    --config_file CONFIG_FILE
                            path to Config file for Models
    --debug               Print generated Apptainer command instead of running container, useful for sanity checking

Note this is the best place to check first as if new options are added it will likely be more up to date 
than these docs.

Many of these options also have there own sub options, for example run, these can be displayed with:

.. code-block:: bash

    ml-toolkit run -h

    usage: run_container.py run [-h] model_name cmd [cmd ...]

    positional arguments:
    model_name  Name of Model to use
    cmd         Command(s) to run

    options:
    -h, --help  show this help message and exit