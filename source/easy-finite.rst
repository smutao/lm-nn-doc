.. _easy-finite:

.. toctree::
   :hidden:

========================
Finite Difference Method 
========================


Above tutorials on performing local mode analysis with LModeA-nano all require the calculation of the full Hessian matrix, then the local stretching force constant can be calculated via analytical formula under the hood. In the following,     a finite difference method to obtain the same   local stretching force constant is introduced for the following situations/purposes

* The chemical structure is too large to have its full Hessian matrix calculated in an efficient manner.  In addition, there is only one or two chemical bonds of interest to be analyzed in this system.

* In order to understand local mode analysis in a straightforward manner. 


Follow these steps

* optimize the molecular or primitive cell structure to a local minimum point, record the total energy as   :math:`E_{(r_0)}`

* find the chemical bond of interest, its bond length is :math:`{r_0}`

* lengthen this bond by a tiny amount :math:`{\Delta r}` (e.g. 0.005 Å), fix this bond length or fix the Cartesian coordinates of these two bonded atoms, optimize the remaining structure (including cell parameters in the case of solid), record the final total energy as  :math:`E_{(r_0+\Delta r)}`

* shorten this bond by a tiny amount :math:`{\Delta r}` (e.g. 0.005 Å), fix this bond length or fix the Cartesian coordinates of these two bonded atoms, optimize the remaining structure (including cell parameters in the case of solid), record the final total energy as  :math:`E_{(r_0-\Delta r)}`

* the approximate local stretching force constant :math:`k_n^a \approx (E_{(r_0+\Delta r)} - 2 E_{(r_0)} + E_{(r_0-\Delta r)})/{\Delta r}^2`

Conversion factors to mdyn/Å are 

* 1 Hartree/:math:`Å^2` = 4.3597 mdyn/Å
 
* 1 eV/:math:`Å^2` = 0.1602 mdyn/Å



|

|

|

|




