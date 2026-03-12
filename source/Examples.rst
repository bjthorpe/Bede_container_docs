.. _Examples:

ML Atomic Potentials 
====================

Using MatBench Discovery Models
-------------------------------
`Matbench Discovery`_ is an interactive leaderboard which ranks 
popular ML models for inter-atomic potentials. Definition 
files for the top 20 models [1]_ can be found in the ``Images``
directory. These are also all setup with corresponding config files in
``Container_Configs/MatBench_Discovery`` such that you can 
simply refer to them by name.

.. [1] At time of writing (Dec 2025)
.. _Matbench Discovery: https://matbench-discovery.materialsproject.org/

To use a model, for example the current number 
one model, eSEN-30M-OAM, we first need to load the model with:

.. code-block:: bash

    ml-toolkit load eSEN-30M-OAM

This will download some files and create a Container image 
``eSEN-30M-OAM.sif`` in the ``Images`` directory. 
Note: this only needs to done the first time you use the model.

Once this is complete we can run something in the container using:

.. code-block:: bash

    ml-toolkit run eSEN-30M-OAM $COMMAND

Where $COMMAND is the command you would like to run inside the container.

In this example we will predict the atomic potential of a hydrogen 
molecule then use this to calculate the charge density.

Using the Atomic Simulation Environment
***************************************
The vast majority of Models listed on Matbench Discovery 
can be used through python via the Atomic Simulation 
Environment (`ASE`_).

.. _ASE: https://ase-lib.org/

In principle to get the predicted atomic potentials 
we simply need to setup an ASE calculator then use the 
``get_potential_energy()`` method. The problem, however,is 
every model uses it's own calculator with there own setup steps.

To make things easier in the Examples directory we have provided a 
python function ``initialise_model()`` that takes in a string that
is the name of the model you wish to use and returns an appropriate
ASE calculator for that particular ML model.

As such the following is an example of how to predict and extract the potential
of a hydrogen molecule and using ASE and MatterSim:

.. code-block:: python

    # Example script to get potential of H2 molecule from with MatterSim through ASE
    # This should be run inside the MatterSim container.

    from Matbench_Models import initialise_error, initialise_model
    from ase import Atoms
    from ase.optimize import BFGS
    from ase.calculators.nwchem import NWChem
    from ase.io import write

    # Setup the system with ASE, in this case a simple H2 molecule
    h2 = Atoms('H2', positions=[[0, 0, 0],[0, 0, 0.7]])

    # Tell ASE to use MatterSim as a Calculator
    h2.calc = initialise_model('MatterSim')

    # Do the calculations 
    opt = BFGS(h2)
    opt.run(fmax=0.02)
    write('H2.xyz', h2)
    h2.get_potential_energy()

This should be run with the bede_ml-toolkit as:

.. code-block:: bash
    # change directory to to wherever bede_ml-toolkit is installed
    cd $ML_TOOLKIT_HOME
    # This only needs to be done the first time
    ml-toolkit build MatterSim
    
    ml-toolkit run MatterSim python3 scripts/Examples/H2_MatterSim.py

From here you could do some further analysis with ASE or convert the data for use 
with another tool. In our case we will move on to calculating charge density 
using CASTEP.

Interfacing with Castep
-----------------------

CASTEP can use an external routine to perform energy, force and stress calculations 
using ML models in two distinct ways:

1. The "File" method
2. The "Server" method

At this stage users should note that both of these methods are **still considered experimental**. 
As such they use a **developer keyword**. The current plan is to release them as an **official keyword in CASTEP 27**.

The File Method
***************

Conceptually, this is the simplest approach. Whenever CASTEP need to update the energy,
forces, and stresses it writes the current geometry to a .cell file. It then calls an 
external process. This process reads in the .cell file evaluate the energy, force 
and stress, writing the results to a .geom file. CASTEP then reads in this .geom file 
and carries on it merry way.

We note that Whilst it's simple, this approach has a non-trivial overhead and likely 
will not scale well to more than a few mpi-processes. As such it is only really recommended 
for small scale testing. It is however the **only option available** if you are using 
**CASTEP version 25 or older**.

The Server Method
*****************

The method is **new to CASTEP V26** (the latest version at the time of writing). It involves
running a stand-alone application which both: evaluates the energy, force and stress; and 
acts as a network server. CASTEP can then talk to this application over the network and 
request updated values for energy, force and stress as needed. 

This is more a bit more complex to setup but has three major advantages:

1. You can run multiple servers at once allowing you to easily scale over many mpi mpi-processes. 
2. I uses the network for communication so the overhead will be minimal.
3. It's more robust, the server and CASTEP are completely independent. As such if one or the other 
   encounters a problem and crashes they can easily be restarted and start back from an earlier 
   point in time.

CASTEP Examples
---------------

We assume users already have some familiarity with CASTEP. Thus we will not be covering the 
basics of how to use it, or any of the theory behind these calculations. If you need this 
CASTEP already has `extensive documentation`_ and we will adapting several of the tutorial 
examples to work with ML potentials.


**Note to Kit and Scott:** I have compiled a version of CASTEP 26 on Bede for testing using the nvidia compiler 
with both serial and OpenMPI. Also it is the latest GPU branch so if you really want to recompile 
go for it. However, I kept it CPU only as I did not want the additional complications with OpenACC.

It's under `/projects/bdyrk04/castep26` you will need to load nvhpc/24.1 and openmpi/4.1.6.
You can ignore the system version of OpenBLAS and fftw, as for whatever reason they are only available for gcc. NVHPC 
uses its own blas but only provides cufft which is GPU only so I complied my own fftw under `/projects/bdyrk04/libs/lib`.

Charge density of Si lattice (file method)
******************************************

Bandstructure of Graphene (server method)
*****************************************

Si GA (server method)
*********************