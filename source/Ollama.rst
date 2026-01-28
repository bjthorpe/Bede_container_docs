DIY Large Language Model (llm)
==============================

Cowsay is a bit of fun but now lets try something a bit more interesting. 
We are going to create our own custom configuration for an llm based 
chatbot using Ollama. At first this may sound intimating, in practice 
however, this is surprisingly straightforward.

To start we need two things:

1. a container config (.yaml) file
2. a container definition

Writing a Container config (.yaml) file
---------------------------------------

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
---------------------

Next we need a container definition. This is a blueprint that tells Apptainer how to create 
our container. For this we have two options:

1. create our own .def file
2. download a definition from an external container repository

In our case we will use option 2 as creating a definition file
is quite involved. Especially with software as complex as Ollama.

Apptainer can access several different container repositories, 
such as Container Library and ORAS.

However, for this we will use `dockerhub`_ which is the public container
repository which is commonly used for a different container software called `docker`_. 

This is by far and away the most popular container repository with
official definitions for building containers to work with a huge 
amount of popular software. Including Ollama.

.. _dockerhub: https://hub.docker.com/
.. _docker: https://www.docker.com/
.. _Ollama: https://ollama.com/

To use the definition from dockerhub with our container we will need to update the config file.