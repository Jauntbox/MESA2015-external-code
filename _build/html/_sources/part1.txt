Part 1 - Compiling MESA code outside of MESA
============================================

.. 
	In this part we'll learn how to setup a makefile to compile code that uses the MESA modules in your own code``

Many of the MESA modules have example code on how to use the module outside of MESA. We'll start with the equation of state (EOS) module, which lives in the directory:

.. code-block:: bash

   $MESA_DIR/eos
   
First, make a local directory where you'd like to store your code and navigate to this new folder.

.. code-block:: bash

   mkdir eos_local
   cd eos_local
   
Next, copy over the source code file we'll work with as well as a makefile (a set of rules for how to make sure the compilation links up to the correct libraries)

.. code-block:: bash

   cp $MESA_DIR/eos/test/src/sample_eos.f .
   cp $MESA_DIR/eos/test/make/makefile .
   
We compile the code by typing the word ``make``, but right now it won't work since there's some vestigal code in the makefile that refers to relative paths within the MESA directory structure. Specifically, you should see the error:

.. code-block:: bash

   makefile:9: ../../../utils/makefile_header: No such file or directory
   make: *** No rule to make target '\../../../utils/makefile_header'.  Stop.
   
This tells us that the makefile is looking for something in a relative path. We can fix this error by commenting out the first instruction in the makefile:

.. code-block:: bash

   #MESA_DIR = ../../..
   
We also need to tell the makefile where to find the library files corresponding to the MESA modules. Replace the lines in *Step 2* of the makefile with:

.. code-block:: bash

   LOAD_MESA = $(LOAD_MESA_STAR) $(LOAD_EXTRAS)
   
If we try compiling again, it will still give an error:

.. code-block:: bash
   
   make: *** No rule to make target `eos_support.o', needed by `plotter'.  Stop.
   
This tells us the makefile has rules for compiling files we don't have - there is no ``eos_support`` file in our folder anymore. The easiest change is to remove everything in *Step 3* of the makefile except the two lines

.. code-block:: bash

   SAMPLE = sample
   SAMPLE_OBJS = sample_eos.o
   TEST_DIR = .
	
   $(SAMPLE) : $(SAMPLE_OBJS)
   	$(LOADER) $(FCopenmp) -o $(TEST_DIR)/$(SAMPLE) $(SAMPLE_OBJS) $(LOAD_MESA)
      
Finally, you should be able to compile the ``sample_eos.f`` file and get an executable out called ``sample``, which you can run by typing

.. code-block:: bash

   ./sample
   
And runtime error... One last edit is necessary! You should get the error:

.. code-block:: bash

   open failed for ../../data/version_number
   please check that mesa_dir is set correctly
   const_init failed
   STOP 1
   
There's one last relative path in the source code itself. Find the line near the beginning of the setup 

.. code-block:: fortran

   my_mesa_dir = '../..'
   
and replace the relative path with the absolute path to your mesa installation. Hopefully, you should now get some output from querying the EOS with a given density, temperature, and composition.

