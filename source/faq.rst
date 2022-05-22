.. _faq:

.. toctree::
   :hidden:


==========================
Frequently Asked Questions
==========================



**Q**:  I have never heard about nor used this method about local vibrational mode theory. Is this method reliable in measuring the chemical bond strength? Will I get trouble if I apply this method in my research project? 


The force constant defined in the local vibrational mode theory is a reliable quantitative measure for chemical bond strength (especially for bonds of similar types). 

After years of exploration, testing and application, this method has been extensively used by us and peer researchers to study various types of covalent bonds and non-covalent interactions (e.g. hydrogen/halogen bond). Our recent review article has listed the applications of the  local vibrational mode theory as of April 2020.

Noteworthy is that Prof. Gernot Frenking who is an expert on energy decomposition analysis (EDA) recently published an article titled *The Strength of A Chemical Bond* on  *Int. J. Quantum Chem.* with co-workers. He pointed out that "… the force constant is the **most general measure** for determining the strength of a chemical bond in molecules" and considered the local vibrational mode theory as "… the **best broadly applicable method** for estimating the strength of chemical bonds that is presently available."

**Q**: Could the local vibrational modes be experimentally measured in labs? 

Local vibrational modes are generally not measurable in experiments except for situations like diatomic molecules, isotope substitution and overtone. This is because vibrational spectroscopy measures normal vibrational modes. However, one may consider one normal vibrational mode as a mixture of local vibrational modes. 




**Q**: Is structure optimization a must before local mode analysis? Can I proceed even if there are some imaginary frequencies? 


It is **required** to optimize the structure to a local minimum point. 

If imaginary frequencies are found in vibrational analysis, one should not proceed to do local mode analysis. Try to eliminate the imaginary frequencies. This implies that current implementation of local mode analysis in LModeA-nano cannot be applied to analyze transition-state (TS) structure.

However, be cautious that some translational or rotational modes  might have minor imaginary frequency values. 



**Q**: Is cell optimization  a must before local mode analysis for solids? Can I only optimize the Cartesian coordinates of atoms in a solid? 


Yes, the complete cell optimization should be conducted. For example, in VASP program the ``ISIF=3`` should be specified. 


**Q**: Can I apply the local mode analysis to a system whose structure is partially optimized?

For a molecular system where some atoms are fixed during geometry optimization, one can first apply the generalized subsystem vibrational analysis (GSVA) implemented in the UniMoVib package to extract the effective Hessian matrix for the relaxed portion of the molecule (excluding the fixed atoms). Then one can use LModeA-nano to analyze the bond strength for the relaxed portion of this structure with the effective Hessian matrix. 

For solid system (or periodic systems in general), GSVA is not extended yet. 


**Q**: Does the consistency in the level of theory matter? Can I calculate one bond force constant at level A and calculate the force constant of a similar bond at level B, then directly compare their values? 

While performing local mode analysis, the consistency in the level of theory should be guaranteed. 

In other words, it is preferred to calculate a series of structures with the same level of theory in one program.  



**Q**: I got some force constant value by selecting the H...H bond in a water molecule, is this result correct? 

One should always be aware of what he/she is calculating in computational chemistry.  The local stretching force constant should ONLY be calculated for two atoms between which there exists a chemical bond or non-covalent interaction. One should NEVER select two random atoms and interpret corresponding force constant as bond strength. 


**Q**: Can I apply the local vibrational mode theory to measure the bond strength between two metal atoms?

If these two metal atoms exist in elemental metals (metallic solids consisting of only one element), it is not possible to apply the local vibrational mode theory because the phonon frequencies of the primitive cell at Gamma point are all zeros. 

For other systems like metal clusters and metal oxides, one may apply the local vibrational mode theory to measure the bond strength between two metal atoms. 



**Q**:  As only the primitive cell structure is required for local mode analysis in solids, does the k-point sampling matter? 

Yes, in order to get reasonable value, the  k-point sampling of the first Brillouin zone should be sufficient.  




**Q**: LModeA-nano gives force constant and frequency as output, which one should I used for chemical bond strength measure? 

In most situations, one should choose force constant because it is independent of atomic masses.

**Q**: I got some error when selecting atoms for an angle in a solid structure? How can I handle this? 

For an angle definition, it needs three atoms to be picked. Try pick the first atom within the primitive cell. :-)

**Q**: I got some error message when I load in the second structure after I analyze the first one. How to handle this issue?

One needs to close the current PyMOL window after analyzing one structure and reopen a new PyMOL window for another structure. 


