.. ML_Toolkit documentation master file, created by
   sphinx-quickstart on Mon Nov 24 14:46:00 2025.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to ML_Toolkit documentation
===================================

Introduction:
-------------
ML_Toolkit is a python script designed to make working with ML/AI models 
simpler and easier on the Bede grace hopper nodes by using containers. 

These docs apply to version 0.9 (tagged as V0.9 on github) 

Containers
----------

What are they?
**************
If you are unfamiliar with containers, here is a quick overview from opensource.com:

    “Containers, in short, contain applications in a way that keep them isolated from 
    the host system that they run on. Containers allow a developer to package up an 
    application with all of the parts it needs, such as libraries and other dependencies, 
    and ship it all out as one package. And they are designed to make it easier to provide 
    a consistent experience as developers and system administrators move code from development 
    environments into production in a fast and replicable way.

    In a way, containers behave like a virtual machine. To the outside world, they can look 
    like their own complete system. But unlike a virtual machine, rather than creating a 
    whole virtual operating system, containers don’t need to replicate an entire operating 
    system, only the individual components they need in order to operate. This gives a 
    significant performance boost and reduces the size of the application. They also operate
    much faster, as unlike traditional virtualization the process is essentially running 
    natively on its host, just with an additional layer of protection around it.”

Why do we use them?
*******************

We have chosen containers as a way of interfacing with AI/ML models on Bede for a number of reasons:

    1. We, the developers, take on the effort to ensure that all software dependencies are met meaning 
    that the users can focus on getting up and running as quickly as possible.

    2. Containers are portable. This means that, whether working on a laptop or a HPC cluster, a 
    container pull (or download) is usually all that’s required and users’ workflows can be easily moved 
    from machine to machine, such as when scaling up to a larger resource.

    3. They do not require you to rig-fence entre sections of your hardware for a single process, such as a chunk of cpu cores or ram.

    4. They can more easily make use of hardware acceleration with GPUs.

    5. They allow us to install external modules each with their own dependencies isolated from one 
    another. You can also easily lock them to extremally specific versions of software so that they will not 
    suddenly break when external libraries are updated.

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   Getting_started
   Examples
   Ollama
   Options
   Developer
   
   
