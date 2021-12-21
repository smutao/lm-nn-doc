.. _faq:

.. toctree::
   :hidden:


==========================
Frequently Asked Questions
==========================



**Q**: Is structure optimization a must before local mode analysis? Can I proceed even if there are some imaginary frequencies? 


It is **required** to optimize the structure to a local minimum point. 

If imaginary frequencies are found in vibrational analysis, one should not proceed to do local mode analysis. Try to eliminate the imaginary frequencies. This implies that current implementation of local mode analysis in LModeA-nano cannot be applied to analyze transition-state (TS) structure.

However, be cautious that some translational or rotational modes  might have minor imaginary frequency values. 



**Q**: Is cell optimization  a must before local mode analysis for solids? Can I only optimize the Cartesian coordinates of atoms in a solid? 


Yes, the complete cell optimization should be conducted. For example, in VASP program the ``ISIF=3`` should be specified. 




**Q**: Does the consistency in the level of theory matter? Can I calculate one bond force constant at level A and calculate the force constant of a similar bond at level B, then directly compare their values? 

While performing local mode analysis, the consistency in the level of theory should be guaranteed. 

In other words, it is preferred to calculate a series of structures with the same level of theory in one program.  



**Q**. I got some force constant value by selecting the H...H bond in a water molecule, is this result correct? 

One should always be aware of what he/she is calculating in computational chemistry.  The local stretching force constant should ONLY be calculated for two atoms between which there exists a chemical bond or non-covalent interaction. One should NEVER select two random atoms and interpret corresponding force constant as bond strength. 




**Q**:  As only the primitive cell structure is required for local mode analysis in solids, does the k-point sampling matter? 

Yes, in order to get reasonable value, the  k-point sampling of the first Brillouin zone should be sufficient.  









