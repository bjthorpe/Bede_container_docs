Developer Notes
==================
.. _Developer:

Reporting any issues
--------------------

if you have any issues with the software or any feature requests feel free to report them on the 
`git hub page`_ or email me (ben.thorpe@york.ac.uk).

.. _github page: https://github.com/bjthorpe/Bede_containers

Debugging tips:
---------------
If you need to debug containers, a good place to 
start is the log file under :code:`logs/log.log`. This 
contains more detailed output and lots of other 
useful information.

However, The following may also be helpful:

There is a :code:`--debug` cmd option. This can 
be used to perform a "dry-run" as it simply runs
through all the python code. Only instead of starting 
or (building) the container, it prints the underlying 
Apptainer command. 

This can be useful for checking if an error is in the 
python code or Apptainer itself.

The :code:`apptainer shell` command used as:

.. code-block:: bash

    apptainer shell my_amazing_container.sif 
    
is also very useful for debugging containers.

It simply starts the container and then drops 
you into a bash shell. This can be useful if 
you want to poke around and see what's going on.

You can also jump into an already running container using:

.. code-block:: bash
    
    apptainer shell instance://container_name

Note: if you need the container name you can list all running containers with:

.. code-block:: bash

    apptainer instance list

Notes on debugging background containers
----------------------------------------
when debugging containers that are running as background instances. That is
containers running with :code:`apptainer instance start ...` or our wrapper
:code:`./ML_toolkit start ...`.

It may be useful to know Apptainer (unhelpfully) redirects all output and 
errors from containers running as instances in the background to a log 
file in the users home directory, under 
:code:`$HOME/.apptainer/instance/logs/$HOSTNAME/$USER`. 

Annoyingly this is not mentioned in the documentation and cannot be changed. 
Also any attempts at output redirection such as :code:`command &>> output.txt`
get put into a file in this directory. So if you need the output for debugging 
ect this is where it lives.
