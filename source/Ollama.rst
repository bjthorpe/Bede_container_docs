Did Anyone ask for a DIY AI chatbot?
====================================

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
in the Container_Configs directory

.. code-block:: yaml

    # This is an Ollama Container config to test with
    Ollama_Test_Container:
        description: 'latest version of ollama pulled from dockerhub, for testing'
        group: Test
        container_definition: "ollama/ollama:0.13.1-rc0"


Container Definitions
---------------------

Next we need a container definition. As mentioned in the 
last step. This is a blueprint that tells Apptainer how to create 
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