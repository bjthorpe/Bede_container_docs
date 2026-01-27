Examples
========

Using MatBench Discovery Models
-------------------------------
`Matbench Discovery`_ is an interactive leaderboard which ranks 
popular ML models for inter-atomic potentials. Definition 
files for the top 20 models [1]_ can be found in the ``Images``
directory. These are also all setup in a corresponding config file
``Container_Configs/MatBench_Discovery.yaml`` such that you can 
simply refer to them by name.

.. [1] At time of writing (Dec 2025)
.. _Matbench Discovery: https://matbench-discovery.materialsproject.org/

To use a model, for example the current number 
one model, eSEN-30M-OAM, we first need to load the model with:

.. code-block:: bash

    ML_Toolkit load eSEN-30M-OAM

This will download some files and create a Container image 
``eSEN-30M-OAM.sif`` in the ``Images`` directory. 
Note: this only needs to done the first time you use the model.

Once this is complete we can run something in the container using:
.. code-block:: bash

    ML_Toolkit run eSEN-30M-OAM $COMMAND

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

This should be run with the ML container toolkit as:

.. code-block:: bash

    # This only needs to be done the first time
    python3 main.py build MatterSim
    
    python3 main.py run MatterSim python3 Examples/H2_MatterSim.py

From here you could do some further analysis with ASE or convert the data for use 
with another tool. In our case we will move on to calculating charge density 
using CASTEP.

Interfacing with Castep
***********************


