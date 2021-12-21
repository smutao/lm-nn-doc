.. _solid-model:
..    include:: <isonum.txt>
.. toctree::
   :hidden:


=============================
Solid-state Modeling Packages
=============================

To obtain the Hessian matrix from some solid-state modeling packages, the latest version of `Phonopy <https://phonopy.github.io/phonopy/>`_ package needs to be installed first. 


.. note::
   Although the following examples are on the calculation of solid systems with different solid-state modeling packages, this does not mean that molecular systems calculated by these packages cannot be analyzed by LModeA-nano. 
   
   In fact, molecular systems calculated by solid-state modeling packages can be analyzed in the same way as solids except that the **Dimensions** needs to be set to ``0`` in LModeA-nano.



VASP
-----

Tested with VASP 5.4.4.

.. _vasp-method-1:

Method 1
^^^^^^^^^^^^^^^

The first step is cell optimization. For 3D solid system, an example ``INCAR`` file is 

.. literalinclude:: _static/code/vaps-opt.inp
 


After the cell optimization (``ISIF=3``) of a 3D solid structure, the vibrational  analysis with VASP needs to be performed. 

An example ``INCAR`` file for vibrational analysis is

.. literalinclude:: _static/code/vaps-freq.inp

The most critical parameters are 

*  ``IBRION=5`` - finite difference method to calculate the Hessian matrix without considering symmetry

*  ``NFREE=2`` - use central differences

*  ``POTIM=0.005`` - step size for finite difference in Å

When the vibrational analysis calculation is done, check the lowest frequencies by  ::

 $ grep cm OUTCAR | tail -n 6
 115 f  =    0.899811 THz     5.653679 2PiTHz   30.014464 cm-1     3.721321 meV
 116 f  =    0.729527 THz     4.583752 2PiTHz   24.334394 cm-1     3.017081 meV
 117 f  =    0.483556 THz     3.038272 2PiTHz   16.129693 cm-1     1.999828 meV
 118 f/i=    0.013493 THz     0.084780 2PiTHz    0.450086 cm-1     0.055804 meV
 119 f/i=    0.036011 THz     0.226262 2PiTHz    1.201188 cm-1     0.148928 meV
 120 f/i=    0.056606 THz     0.355667 2PiTHz    1.888177 cm-1     0.234104 meV

Such result shows that the optimization is successful because the last three frequencies (close to zero cm :math:`^{-1}`) correspond to the translation modes of the whole primitive unit cell and the remaining frequencies are all positive. 

In the same directory of ``OUTCAR``, another output file ``vasprun.xml`` is also generated. Run::

 $ phonopy --fc vasprun.xml
 ...
 FORCE_CONSTANTS has been created from vasprun.xml.
 ...

Two files are needed for later use

* ``POSCAR`` - input structure for vibrational analysis calculation

* ``FORCE_CONSTANTS`` - Hessian matrix extracted by Phonopy from .xml file 

Compose an input file ``vasp.inp`` for LModeA-nano 

.. code:: text

   @VASP
   POSCON = POSCAR
   FCPHONOPY = FORCE_CONSTANTS
   

Following these steps



* open a new PyMOL window and launch LModeA-nano by clicking **Plugin** |rarr| **LModeA-nano**
 
* click the **... (Browse)** button and select the ``vasp.inp`` file


* click **Load**

* change **Dimensions** to ``3`` and click **Confirm** button

* change the supercell size percentage from ``110%`` to ``140%`` or other values, and click **Partial Supercell** button 


Then local mode analysis can be performed as described in  :ref:`quickstart examples <quick-example>`.  

.. _vasp-method-2:

Method 2 - For large cells
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For primitive unit cells having more than 70 atoms, the calculation of Hessian matrix by finite difference method can be time-consuming because it requires 6N  force evaluation steps (N is number of atoms in the cell). The above *Method 1* performs all force evaluations on a single computational node. If there is a computational cluster, the following method is faster because it may distribute the force evaluations to different nodes. 


After the cell optimization (``ISIF=3``) of a 3D solid structure, save the ``CONTCAR`` file as ``POSCAR`` in a new directory.


.. /users/yunwent/scratch/halo-vasp/40-1588334/i2/freq/test-phonopy-numerical-hessian


Run Phonopy ::

  $ phonopy -d --dim="1 1 1" --pm --amplitude 0.005 
  ...
  Creating displacements
  Plus Minus displacement: full plus minus directions
  Settings:
  Supercell: [1 1 1]
  ...
  
A series of displaced unit cell structure are generated: ``POSCAR-001``, ``POSCAR-002``, ``...``. Each structrue will have its forces evaluated in a single VASP calculation.  

The ``INCAR`` file for force evaluation can look like 

.. literalinclude:: _static/code/vaps-force.inp
 
It is recommended to create a separate directory for each structure ::

  for id in POSCAR-*
  do
    mkdir disp-${id} 
    cp ${id} disp-${id}/POSCAR
    cp POTCAR KPOINTS INCAR  disp-${id}/
  done   
   
When submitting the VASP calculations, each computational node can handle a few structures, like :: 

  for i in $(seq -f "%03g" 1 100)
  do
    echo disp-POSCAR-${i}
    cd disp-POSCAR-${i}
    vasp_std > vasp-${i}.out 
    cd ..
  done   
  
``seq -f "%03g" 1 100`` means to loop over 001 to 100.
  
After the force evaluation of all displaced structures are finished, create ``FORCE_SET`` using Phonopy ::

  $ phonopy -f disp-POSCAR-{001..xxx}/vasprun.xml
  
``xxx`` is the total number of displaced structures  

Then use Phonopy to create ``FORCE_CONSTANTS`` file ::
  
  $ phonopy --dim="1 1 1" --writefc     
  
With ``POSCAR`` and ``FORCE_CONSTANTS`` files, the local mode analysis can be performed with LModeA-nano like in  :ref:`Method 1 <vasp-method-1>`.


CP2K
-----

Tested with CP2K 8.2. The input file template can be generated by Multiwfn package.

Method 1
^^^^^^^^^

The input file for cell optimization of 3D solid is like

.. literalinclude:: _static/code/cp2k-opt.inp

With the optimized cell structure, the vibrational analysis is required

.. literalinclude:: _static/code/cp2k-freq.inp

Critical parameters include

* ``DX 0.008`` - this specifies the step size of finite difference

* ``HESSIAN high`` - this prints the Hessian matrix in the CP2K output file

  
After the vibrational analysis is done, check the vibrational frequency values to make sure the structure is at a local minimum energetically. 


Compose an input file ``cp2k.inp`` for LModeA-nano ::

  @cp2k
  
  mode = native
  OUTPUT = k2reh9-refine-freq.log
  INPUT = k2reh9-refine-freq.inp  
  
The ``INPUT`` and ``OUTPUT`` specify the input and output files of vibrational analysis calculation.


Follow these steps to perform local mode analysis with LModeA-nano



* open a new PyMOL window and launch LModeA-nano by clicking **Plugin** |rarr| **LModeA-nano**
 
* click the **... (Browse)** button and select the ``cp2k.inp`` file

* change the program drop-down list from **VASP** to **CP2K**

* click **Load**

* change **Dimensions** to ``3`` and click **Confirm** button

* change the supercell size percentage from ``110%`` to ``140%`` or other values, and click **Partial Supercell** button 


Then local mode analysis can be performed as described in  :ref:`quickstart examples <quick-example>`.  



Method 2 - For large cells
^^^^^^^^^^^^^^^^^^^^^^^^^^^


Similar to :ref:`Method 2 with VASP <vasp-method-2>`, the general idea of this method is ask Phonopy package to generate the CP2K input files for the displaced structures starting from an already optimized structure. Then force evaluations are performed on these displaced structures in order to obtain Hessian. 


A template CP2K input file having the optimized cell structure is needed, and the format of this input should look like the following. Otherwise, Phonopy package cannot parse it correctly. More examples of such CP2K input can be found in the Phonopy repo on GitHub: `NaCl <https://github.com/phonopy/phonopy/blob/develop/example/NaCl-CP2K/NaCl.inp>`_ and `Si <https://github.com/phonopy/phonopy/blob/develop/example/Si-CP2K/Si.inp>`_.


.. literalinclude:: _static/code/cp2k-template.inp


Then run :: 

  $ phonopy --cp2k -d --dim="1 1 1" --pm --amplitude 0.005 -c na2reh9-template.inp 
  
The parameter after ``-c`` is the above-mentioned CP2K template input file containing the optimized structure.  Phonopy generates a series of CP2K input files for force evaluation: ``na2reh9-template-supercell-001.inp``, ``na2reh9-template-supercell-002.inp``, ``...``. This step will also generate a ``phonopy_disp.yaml`` file.

The generated CP2K input can look like 

.. literalinclude:: _static/code/cp2k-generated.inp


After the force evaluation for these generated input files is completed, it creates a series files containing the force information: ``na2reh9-phonopy-supercell-001-forces-1_0.xyz``, ``na2reh9-phonopy-supercell-002-forces-1_0.xyz``, ``...`` 

Make sure that these ``.xyz`` files are collected in one directory and this directory should contain ``phonopy_disp.yaml`` file.

Run Phonopy to create the ``FORCE_SETS`` file :: 
 
  $ phonopy --cp2k -f na2reh9-phonopy-supercell-{001..xxx}-forces-1_0.xyz

``xxx`` is the total number of displaced structures generated by Phonopy

Then use Phonopy to generate the ``FORCE_CONSTANTS`` file :: 
  
  $ phonopy --dim="1 1 1" --writefc
  
Compose the input file ``cp2k.inp`` for LModeA-nano :: 

  @cp2k

  mode = phonopy
  FCPHONOPY = FORCE_CONSTANTS
  INPUT = na2reh9-template.inp
  
The ``INPUT`` parameter specifies the template CP2K input file containing the optimized structure.   
  






Quantum ESPRESSO
----------------

Tested with QE 6.4.1.

There are two ways to do local mode analysis on the results from QE. The first one is to utilize the density functional perturbation theory (DFPT) implemented in QE to obtain the force contant matrix at Gamma point (q=0). The other method known as “frozon phonon” is to use finite difference approach to generate a series of force sets in order to construct the Hessian matrix by using Phonopy package.

These two approaches are equivalent in principle. While the DFPT method in QE has limited support for levels of theory (e.g. hybrid density functional), one has to use the finite difference approach if DFPT is not applicable. Therefore, it is recommended to employ the frozon phonon method.


Suppose that users have obtained the optimized primitive cell structure of a solid, the following tutorial skips this cell optimization step.  


.. _qe-dfpt:

DFPT 
^^^^^

This method includes three steps

#. SCF calculation with ``pw.x``

#. DFPT calculation with ``ph.x``

#. generate force constant matrix with ``q2r.x``


Based on an optimized primitive unit cell structure, prepare an SCF input file ``diamond-scf.inp``


.. literalinclude:: _static/code/qe-scf.inp

The ``CELL_PARAMETERS`` and ``ATOMIC_POSITIONS`` have to be in the unit of ``angstrom``.


Run DFPT calculations. The program to run this job is PHonon (``ph.x``). The input file needs to specify the same ``outdir`` and ``prefix`` as the SCF job did. In addition, specify the Gamma point (q=[0,0,0]) in the end of the input file. 


.. literalinclude:: _static/code/qe-dfpt.inp


The ``fildyn`` specifies the name of the dynamical matrix file as an output.

Create a text file named ``dynmat.dyn0`` :: 

  1 1 1
  1
  
Run ::

  $ cp dynmat.dyn1 dynmat.dyn


Prepare the input file for ``q2r.x``


.. literalinclude:: _static/code/qe-q2r.inp

In this input file, the ``zasr`` controls the the *acoustic sum rules*. For solid systems, we need to set ``zasr`` to ``'simple'`` or ``'crystal'``. For one-dimensional polymer systems, we need to set ``zasr`` to ``'one-dim'``. For isolated molecular systems, we need to set ``zasr`` to ``'zero-dim'``. 

The parameter ``flfrc``  specifies the name of the interatomic force constant file generated from the calculation of ``q2r.x``.


So far, we have obtained necessary data files for local mode analysis

* ``diamond-scf.inp`` - input file for SCF calculation

* ``diamond.fc`` - the force constant file specified by ``flfrc`` 


Compose an input file ``qe.inp`` for local mode analysis :: 

  @QE
  mode = dfpt
  GeomIn = diamond-scf.in
  q2rFC = diamond.fc

Follow these steps to perform local mode analysis with LModeA-nano



* open a new PyMOL window and launch LModeA-nano by clicking **Plugin** |rarr| **LModeA-nano**
 
* click the **... (Browse)** button and select the ``qe.inp`` file

* change the program drop-down list from **VASP** to **Quantum ESPRESSO**

* click **Load**

* change **Dimensions** to ``3`` and click **Confirm** button

* change the supercell size percentage from ``110%`` to ``140%`` or other values, and click **Partial Supercell** button 


Then local mode analysis can be performed as described in  :ref:`quickstart examples <quick-example>`.  





Frozon Phonon
^^^^^^^^^^^^^

Given an already optimized cell structure, prepare a template QE input file ``diamond-temp.in``



.. literalinclude:: _static/code/qe-temp.inp

It is important  to set ``tprnfor`` and ``tstress`` as force evaluation is desired to construct Hessian. In addition, the ``atomic positions`` have to be in the ``crystal`` format using the fractional coordinates as required by Phonopy later.   

Then save upper and lower part as two separate files

``diamond-temp-upper.in`` :

.. literalinclude:: _static/code/qe-temp-upper.inp


``diamond-temp-lower.in`` : ::

  K_POINTS automatic
  5 5 5 1 1 1


Use Phonopy to generate displaced structures ::
  
  $ phonopy --qe -c diamond-temp.in -d --dim="1 1 1"


This will generates a series of files containing the displaced structures ``supercell-001.in``, ``supercell-002.in``, ``...``. But these files only contains the structure information. Create complete QE input files with the following script ::

  prefix="supercell-"
  for a in supercell-*.in 
  do
      b=${a#$prefix}
      cat diamond-temp-upper.in $a diamond-temp-lower.in > diamond-${b} 
  done 
  
This script can generate QE input files ``diamond-001.in``, ``diamond-002.in``, ``...``. These input files differ from ``diamond-temp.in`` only in the structure information.    

Run QE ``pw.x`` calculations with these generated input files, the output files are expected to be ``diamond-001.out``,  ``diamond-002.out``, ``...``


Use Phonopy to generate the ``FORCE_SETS`` file ::

  $ phonopy --qe -f diamond-{001..xxx}.out
  
``xxx`` is the total number of displaced structures.  

Generate ``FORCE_CONSTANTS`` file with Phonopy ::

  $ phonopy --writefc
  
Compose a LModeA-nano input file ``qe.inp`` ::

  @QE
  mode = phonopy
  GeomIn = diamond-temp.in
  FCPHONOPY = FORCE_CONSTANTS
   
The remaining step to perform local mode analysis is the same as in :ref:`DFPT method <qe-dfpt>`.







CASTEP
------

Tested with CASTEP 20.11. CASTEP is able to perform phonon (vibrational analysis) calculations with either *DFPT* or *finite difference* method.


Given the already optimized solid structure, prepare the ``.cell`` input for CASTEP


.. literalinclude:: _static/code/castep-cell.txt


Then prepare the ``.param`` file for either DFPT  

.. literalinclude:: _static/code/castep-dfpt.txt



or finite difference calculation

.. literalinclude:: _static/code/castep-finite.txt


The major output   is ``.castep`` file. Then compose an input file  ``castep.inp`` for LModeA-nano.  ::

  @castep

  output = xxxx.castep
  
    


Follow these steps  



* open a new PyMOL window and launch LModeA-nano by clicking **Plugin** |rarr| **LModeA-nano**
 
* click the **... (Browse)** button and select the ``qe.inp`` file

* change the program drop-down list from **VASP** to **CASTEP**

* click **Load**

* change **Dimensions** to ``3`` and click **Confirm** button

* change the supercell size percentage from ``110%`` to ``140%`` or other values, and click **Partial Supercell** button 


Then local mode analysis can be performed as described in  :ref:`quickstart examples <quick-example>`.  











CRYSTAL
--------

Tested with CRYSTAL17. 


To optimize the primitive cell structure, the input file can be 

 

.. literalinclude:: _static/code/crystal17-opt.txt


With the optimized cell, perform the vibrational analysis


.. literalinclude:: _static/code/crystal17-freq.txt


``NUMDERIV`` set to ``2`` means double-sided finite difference while calculating the Hessian. ``STEPSIZE`` sets the step size for finite displacements in Å. 

The calculation produces the main ouput file ``xxxx.out`` and the Hessian file ``HESSFREQ.DAT``.


Then follow the instructions in :ref:`quickstart example <quick-example>`  to do local mode analysis with LModeA-nano.


 


