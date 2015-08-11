Part 2 - Wrtiting code to interface with MESA
=============================================

Now that we can get things compiling outside of MESA, we'll take a look inside the ``sample_eos.f`` file to see how it interfaces with MESA's EOS module. The first thing you should notice is the include statements at the top.

.. code-block:: fortran

      use eos_def
      use eos_lib
      use chem_def
      use chem_lib
      use const_lib
      use crlibm_lib
      
Each module has a library corresponding to a ``def`` (mostly module-level variables declarations, eg. the vector of results you get from querying the EOS module) and a ``lib`` (mostly subroutines that to the actual work of the module). If you want to use a module's variables in your code you'll need a ``use *_def`` statement at the beginning of your code, and if you want to use the subroutines or functions in a module, you'll need a ``use *_lib`` for the appropriate module.

*Why do you think we're using ``const_lib``, but not ``const_def`` here?*

If you want to take a look at the things you have access to in a module, you can look in the two files (eg. in the EOS module)

.. code-block:: bash

   $MESA_DIR/eos/public/eos_def.f90
   $MESA_DIR/eos/public/eos_lib.f90
   
For example, notice the integer ``num_eos_basic_results`` that defines the size of the EOS result arrays ``res``, ``d_dlnd``, ``d_dlnT``, ``d_dabar``, ``d_dzbar`` is not defined in the local code - it's definition lies in ``eos_def.f90``.

The basic structure of the ``sample_eos.f`` code is to initialize all the modules. Most modules, even ``const``, need an explicit call to an initialization subroutine that sets up all the module data, allocates arrays, etc. There are corresponding shutdown subroutines that should be called at the end of your program in order to free the allocated memory.

Mini-exercise 1
---------------

Take a look at the result arrays that are returned by the subroutine ``eosDT_get`` (look at its documentation to figure out what's going on). Some of the data is printed out to the screen already (Pgas, grad_ad, and c_P). Add statements to print out a few other quantities, the specific entropy and eta (the electron degeneracy parameter), as well as dlnS_dlnT.

Mini-exercise 2
---------------
Try the same thing with the net module. Make a directory next to your ``eos_local`` directory and put the ``sample_net`` code in it, compile it, and run it. *hint: you can use nearly the same makefile you used for the EOS code, but you'll also have to change the $(FCfixed) to $(FCfree) in the last line*.