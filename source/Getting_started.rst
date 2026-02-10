Getting started
===============

Initial setup
-------------
The ML_toolkit was primarily built for use on 
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

Once you are logged into the Bede Grace-Hopper node you will need to obtain
the python code using:

.. code-block:: bash

    git clone https://github.com/bjthorpe/Bede_containers
    cd Bede_containers

Next you will need to install some python packages using pip. We recommend using a 
`virtual environment`_ for this:

.. code-block:: bash

    python3 -m venv ML_Toolkit
    source ML_Toolkit/bin/activate
    pip install dacite pyyaml pytest

Optionally you can run the unit tests using:

.. code-block:: bash
    pytest

Getting our bearings
--------------------

The software we downloaded contains a number of different files 
and folders so I think it is worthwhile briefly covering the 
important files/folders and what each of them is used for.

In order to use containers with the ML_Toolkit we need two things:

1. A container config (.yaml) file
2. A container definition
   
Both of these combined will tell ML_Toolkit and Apptainer what software, files 
and steps are needed in order to build our container. We will go into more detail 
about how these are constructed files in later sections. 

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

The only other file that is relevant for now is ML_Toolkit which is the main script used 
to run the software.

Some important/useful directories are:

- **Container_Configs:** Contains all the .yaml config files used to configure the containers.
- **Definitions:** Contains a number of .def files used to build containers.
- **Examples:** Contains various python scripts to demonstrate how to use pre-trained models from MatBench Discovery
- **Images:** Contains the container images (.sif) files used by Apptainer. These store all the data and files used by the container.
- **Models:** Checkpoint Files used for the various pre-trained models.
- **logs:** Log files containing more detailed output from the program, useful for debugging.
- **src:** The python source files used by the program
- **tests:** Various files and scripts used by pytest for conducting unit tests.

Anything else is just used for development and can be safely ignored for now.

With this out of the way we shall now run a small test container to check everything 
is working correctly.

Your first container
--------------------

To test everything is installed correctly we will now build and run a simple 
test container. This will consist of a tiny Linux installation which only has 
a program called `cowsay`_ installed. Cowsay is a small program that generates 
ASCII art pictures of a cow with a message.

.. _cowsay: https://cowsay.diamonds/

An Apptainer defintion file to setup and run this container can be found under 
Definitions/cowsay.def. 

These can be quite involved and a full tutorial on Apptainer is beyond the scope 
of this document. However those interested can consult this `Tutorial`_ or the 
Apptainer `Documentation`_ for a full breakdown of how these files work. 

.. _Tutorial: https://deepwiki.com/apptainer/apptainer-userdocs/2.1-definition-files
.. _Documentation: https://apptainer.org/docs/user/main/definition_files.html

For our purposes however, we can build the container with the following 
command (note this is case sensitive):

.. code-block:: bash

    ./ML_Toolkit build TestContainer

This will download and build the cowsay container from the definition file and create a 
container image TestContainer.sif in the Images directory. A container image contains 
all the files the container needs to run in a format Apptainer can understand.

Once this is complete we can run a command inside this container using:

.. code-block:: bash

    ./ML_Toolkit run TestContainer COMMAND 

Where COMMAND is the Linux command we wish the container to run. In our case this is 
cowsay followed by a message to be displayed, enclosed with double quotes "".

.. code-block:: bash

    ./ML_Toolkit run TestContainer cowsay "It works"

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
    __________
    < It works>
    ----------
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
+ the variables used for each config ML_Toolkit has found
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

    ./ML_Toolkit list

The full list however may mot be that useful as it's quite long. 
As such the --group option may be used to filter down based on the group tag.
For example  

.. code-block:: bash

    ./ML_Toolkit list --group Test

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

    ./ML_Toolkit -h

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

    ./ML_Toolkit run -h

    usage: run_container.py run [-h] model_name cmd [cmd ...]

    positional arguments:
    model_name  Name of Model to use
    cmd         Command(s) to run

    options:
    -h, --help  show this help message and exit