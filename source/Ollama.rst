Defining Your Own Containers: DIY Large Language Model (llm)
============================================================

ML_Toolkit is not just limited to the built in AI/ML models for atomic 
potentials. It can in principle be used for any software.
So Lets try something a bit more interesting.
We are going to create our own custom configuration for an llm based 
chatbot using Ollama. At first this may sound intimating, in practice 
however, this is surprisingly straightforward.

To start we need two things:

1. a container config (.yaml) file
2. a container definition

Writing a Container config (.yaml) file
***************************************

We will start with the container config. Create a new file ollama.yaml 
in the Container_Configs directory and add the following text.

Note: the indentation (using 4 spaces at the start of every line after 
"Ollma_Test_container:") is important.

.. code-block:: yaml

    # This is an Ollama Container config to test with
    Ollama_Test_Container:
        description: 'latest version of ollama pulled from dockerhub, for testing'
        group: LLM
        container_definition:

This is all the information we need to define a simple container. There are many 
other options you can use for more advanced cases, which are documented in 
:ref:`Config Options. <Options>` However, these are the only 3 options that are 
strictly required.

If you are familiar with python programming you may have come across the .yaml format 
before. However put simply it's a way of grouping related data together in a human readable 
hierarchy. In our case we are using it to define basic information about our containers
in a way that ML_Toolkit and Apptainer can understand.

Note: lines starting with a # symbol are known as comments. These lines are ignored by 
the computer and are intended as notes to anyone who is reading the file.

A brief aside:
--------------

You can define multiple containers in a single file by simply un-indenting the entry 
for the next container. A good example of this is in Container_configs/Test_containers.yaml
which defines two different containers, used for testing. This can be useful if you have 
multiple related containers you wish to group together.

Returning to the config file, the first actual line "Ollama_Test_Container" is a human 
readable name we will use with the run command. This can be any combination of letters 
and numbers ending with a colon. However, it cannot contain spaces or special characters. 
It should also ideally be short and vaguely descriptive.

As previously mentioned all lines beyond this point need to be indented. This tells
ML_Toolkit that they are information related to "Ollama_Test_Container". They consist 
of a "field name" followed by a colon then the data related to that field. 

Technically you can use be any number of spaces for the indentation (just not tabs) 
as long as you are consistent throughout the file. In this case I have used 4 spaces.

The first indented line is a brief description to tell other people (and sometimes 
yourself in 6 months time) more information about your container. Note: This is the text
that is displayed when using the list command.

The second line defines a group. This is a simple text label to allow you to group similar 
containers together and filter them when using the list command.

The final line is for the container definition. This can either be the path to local 
a definition file (.def) or a link to an external container repository. Which is a special 
type of website that hosts pre-built containers for people to make use of.

For the moment the definition section has been intentionally left blank so lets 
get into what we need for this.

Container Definitions
*********************

Next we need a container definition. This is a blueprint that tells Apptainer how to create 
our container. For this we have two options:

1. create our own .def file
2. download a definition from an external container repository

In our case we will use option 2 as creating a definition file
is quite involved. Especially with software as complex as Ollama.

Apptainer can access several different container repositories, 
such as Container Library and ORAS.

However, for this we will use `dockerhub`_ which is the public container
repository which is commonly used for a different container software 
called `docker`_. 

This is by far and away the most popular container repository with
official definitions for building containers to work with a huge 
amount of popular software. Including Ollama.

.. _dockerhub: https://hub.docker.com/
.. _docker: https://www.docker.com/
.. _Ollama: https://ollama.com/

To use the definition from dockerhub with our container we will need to
update the config file. 

Links to docker hub come int two parts a repo name and a version tag 
and have the form:

.. code-block:: bash

    docker pull {REPO_NAME}:{TAG}

In our case we don't need the docker pull section just the repo name and tag.
So to find the software we want we will need to search on `dockerhub`_.
In our case Ollama has an official `image`_. To use it we 
will need to update the entry for the container definition  
(not forgetting the 4 spaces at the start) to read:

.. _image: https://hub.docker.com/r/ollama/ollama

.. code-block:: yaml

        container_definition: "ollama/ollama:0.15.2"


This tells ML_Toolkit to get the definition from dockerhub for version 0.15.2 
of Ollama, the latest version available at the time of writing. Note: 
This string we are using comes from the docker pull command. This can generally be 
found at the bottom right of the page (see the red box on the screenshot, 
you may need to scroll down to find it).

.. figure:: images/the_great_sphinx_david_roberts.jpg
    :alt: Screenshot of Ollama page on dockerhub
    :width: 300
    :align: center

    Screenshot of Ollama page on dockerhub, The corresponding docker pull 
    command is located at the bottom right (shown by the red box with 
    number 1). depending on your device you may need to scroll down to
    find it. The blue box (numbered 2) shows where we have set the version tag 
    from the default.

Brief note about tags
---------------------

You may notice we are using a specific version of Ollama. 
By default dockerhub assumes you want the latest available 
version and does not use a tag for this. 

However, it is generally good practice to lock containers on a specific 
version of your software that you know works. This then prevents any 
issues or nasty surprises caused by future updates breaking compatibility.

Omitting the tag also causes issues with Apptainer as if you omit the tag 
Apptainer has no easy way of telling if it's a file or a link to dockerhub.

In our case we selected the exact version we wanted with the dropdown menu
to get the appropriate command (shown by the blue box marked 2 on the screenshot). 
However, if you don't care about the specific version and just want the 
latest one you will need use the tag :latest.

A final note for using dockerhub on Bede
----------------------------------------

There is one final quirk to consider when using containers with dockerhub 
which is specific to Bede. Bede uses the Nvidia GH100 super chips which 
are different to a conventional laptop/desktop PC in that they use 
ARM64 CPUs.

Without going into to much (unnecessary) technical detail they need 
software to be specifically written (built) for them. Dockerhub does 
support builds for ARM64. However, its optional and not all images 
have ARM64 versions.

This is also not helped by the fact it is not obvious at first glance 
which do/don't provide them. The easiest way I've found to get this 
information is look under tags (the green box marked 3). On that page 
there is a column labelled OS/ARCH and you will need to check your 
specific version for **linux/arm64** (not to be confused with the 
similar looking linux/amd64 which are regular laptop/desktop cpus).

.. figure:: images/the_great_sphinx_david_roberts.jpg
    :alt: Screenshot of Ollama page on dockerhub
    :width: 300
    :align: center

    Screenshot of tags page for Ollama on dockerhub, The red box
    shows the OS/ARCH column which should have the information 
    you need. This is a good example as the version tagged as "rocm"
    only supports linux/amd64 whereas we need **linux/arm64** which is 
    included in the version tagged as "latest", and indeed version 0.15.2 
    which is off-screen further down the list.