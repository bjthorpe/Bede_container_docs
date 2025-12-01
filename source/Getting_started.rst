Getting started
===============

Initial setup
-------------
Th ML container toolkit was primarily built for use on 
the Grace-Hopper nodes of N8 HPC cluster Bede. You will
first need to register for an account. Instructions for 
this can be found in the `Bede Documentation.`_

You will also need to ensure you are logged into the 
Grace-Hopper partition `using the ghlogin command.`_

Alternatively if you wish to install the ML toolkit on 
you local machine you can find instructions under the 
:ref:`Developer Notes <Developer>`.

.. _Bede Documentation.: https://bede-documentation.readthedocs.io/en/latest/usage/index.html#using-bede

.. _using the ghlogin command.: https://bede-documentation.readthedocs.io/en/latest/usage/index.html#connecting-to-the-ghlogin-node

Installation instructions
-------------------------

Once you are logged into the Bede Grace-Hopper node you will need to obtain
the python code using:

.. code-block:: bash

    git clone https://github.com/bjthorpe/Bede_containers
    cd Bede_containers

Next you will need to install some python packages using pip. We recommend using a 
`virtual environment`_` for this:

.. code-block:: bash

    python3 -m venv ML_Toolkit
    source ML_Toolkit/bin/activate
    pip install requirements.txt

Once this is complete we shall now run a small test container to check everything 
is working correctly.

Your first container
--------------------

To test everything is installed correctly we will now build and run a simple 
test container. This will consist of a tiny Linux installation which only has 
a program called `cowsay`_ installed. Cowsay is a small program that generates 
ASCII art pictures of a cow with a message.

.. _cowsay: https://cowsay.diamonds/

A script to setup and run this container can be found under Definitions/cowsay.def

It can be built with the following command (note this is case sensitive):

.. code-block:: bash

    ./ML_Toolkit build TestContainer

This will download and build the cowsay container from the script and create a 
container image TestContainer.sif in the Images directory. A container image contains 
all the files the container needs to run in a format Apptainer can understand.

For right now we can run a command inside this container using:

.. code-block:: bash

    ./ML_Toolkit run TestContainer cowsay "It works!!!"

If all has gone to plan you should see the following output:

.. code-block:: bash


Something more interesting:
---------------------------