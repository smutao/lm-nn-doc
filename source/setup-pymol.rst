.. toctree::
   :hidden:


.. _setup-pymol:

========================
 Install PyMOL
========================


Binary from official website
============================

Download PyMOL package from the `official website <https://pymol.org/2/>`_ maintained by Schrödinger. This incentive binary version has bundled Python environment and necessary packages.   

We recommend using this version of PyMOL due to the easy installation.


Pre-built open-source version
=============================

Mac OS users
------------

Latest PyMOL is available in the third-party package managers like Homebrew and MacPorts. See https://pymolwiki.org/index.php/MAC_Install.

For Apple Silicon M1/M2 users, Homebrew is a good choice to install PyMOL via "brew install pymol".


Linux users
-----------

Latest PyMOL can be installed via the native package manager (e.g. yum, pacman) of the Linux distribution. See https://pymolwiki.org/index.php/Linux_Install.

For Ubuntu-based distros (e.g. Ubuntu, Pos OS), PyMOL 2.5+ is available in the software repository for 22.04 LTS and later versions. Therefore, it can be easily installed via "sudo apt install python3-pymol". 

PyMOL from Snap and Flatpak package managers has not been tested and it might have issue when installing LModeA-nano as a plugin.

Windows users
-------------

Christoph Gohlke from UC Irvine provides the pre-built open-source version of PyMOL for Windows on `his website <https://www.lfd.uci.edu/~gohlke/pythonlibs/#pymol-open-source>`_. 

Instructions on how to install this pre-compiled PyMOL can be found at

* https://pymolwiki.org/index.php/Windows_Install#Open-Source_PyMOL 

* https://omicx.cc/posts/2021-04-20-install-pymol-windows/

* https://www.youtube.com/watch?v=B5GbuAND3IQ


For LModeA-nano to function properly, the PyQt5 package should be installed. One may try "pip install pyqt5" if LModeA-nano has problem while launching. 



Compile open-source version
===========================

For Linux users who are familiar with compiling programs, it is possible to build the open-source PyMOL from  `source code <https://github.com/schrodinger/pymol-open-source>`_ on GitHub. Detailed instructions can be found at https://pymolwiki.org/index.php/Linux_Install#Install_from_source.

.. note::
   To make LModeA-nano properly work, the Python environment which compiles the PyMOL source code needs the ``numpy`` package installed.





