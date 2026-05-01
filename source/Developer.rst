Developer Notes
==================
.. _Developer:

Reporting any issues
--------------------

if you have any issues with the software or any feature requests feel free to report them on the 
`github page`_ or email me (ben.thorpe@york.ac.uk).

.. _github page: https://github.com/bjthorpe/Bede_containers

Contributing:
-------------

If you wish to submit any improvements feel free to create a pull request on GitHub. If you 
have models/software you wish to include in the next version please either add them as an 
issue with links to some form of documentation and I'll try my best to implement them 
(though it may take some time as development of this is no longer my full time job). 

Alternatively you can submit a def file and a config file via email (or preferably a pull 
request). However, please ensure they meet the following basic requirements: 

1. The def file builds on X86 and Arm64 systems (preferably Bede, for as long as that system 
   still exists).
2. The model can run without internet access i.e. any checkpoints, model weights ect. are 
   downloaded during the build step and stored inside the container.
3. The container can be built without access to a gpu. 
4. The container uses fixed versions of software packages, preferably using git tags, uv, pip freeze 
   or similar, for maximum reproducibility.

Note I'm happy to discuss/work with you on these if need be just drop me an email.

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

The :code:`--interactive` cmd option used as:

.. code-block:: bash

    ml-toolkit run --interactive ModelName 
    
is also very useful for debugging containers.

It simply starts the container and then drops 
you into a bash shell. This can be useful if 
you want to poke around and see what's going on. [#]_

You can also jump into a continer that is already running in the background using:

.. code-block:: bash
    
    apptainer shell instance://container_name

Note: if you need the container name you can list all running containers with:

.. code-block:: bash

    apptainer instance list

.. [#] you may also wish to combine this with making the container writeable, see next section.

Writeable Containers
--------------------

Note by default containers are stored in .sif files which are read only. 
if you need to make a container contents writeable (say for debugging) 
you can either build it with the --writeable flag as:

.. code-block:: bash

    ml-toolkit build --writeable ModelName 

or you can convert an existing container to/from a writeable container with:

.. code-block:: bash

    ml-toolkit convert ModelName 

This can be a life saver when debugging as it is orders of magnitude quicker 
that re-building the entire container because you forgot to install something.

Note: whilst this option is convenient we only recommend using this for 
debugging and actively advise against using writeable containers in 
production for 3 reasons:

1. Performance will be slower because running containers are stored on disk instead 
   of in system memory.
2. The resulting .sif files will have a smaller size since they can be compressed by the system.
3. Users cannot accidentally/easily mess up containers at runtime.

Notes on debugging background containers
----------------------------------------
when debugging containers that are running as background instances. That is
containers running with :code:`apptainer instance start ...` or our wrapper
:code:`ml-toolkit start ...`.

It may be useful to know that by default Apptainer (unhelpfully) redirects 
all output and errors from containers running as instances in the background 
to a log file in the users home directory, under 
:code:`$HOME/.apptainer/instance/logs/$HOSTNAME/$USER`. 

Annoyingly this is not mentioned in the documentation and cannot be changed.

To solve/mitigate this, in all current container def files, I manually 
redirect stdout and std err to files in the current working directory 
with a fallback of using the logs directory if the current working 
directory is not writeable for whatever reason.

This is reasonably robust but is something you may wish to be aware of when 
writing/debugging new .def files.
