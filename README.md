# aladyn
Artificial Neural Network Molecular Dynamics - Computational Materials mini-app (HPCI)

This mini-app is a simple molecular dynamics (MD) code for performing
constant energy MD simulation using artificial neural networks (ANN)
interatomic potential trained for Al crystal structure.
The trained ANN is produced and offered by Ganga P. Pun and Yuri Mishin
from George Mason University.

-----------------------------------------------------------------------
Requirements:
* Linux
* FORTRAN compiler (2003 or newer)
-----------------------------------------------------------------------
Edit the provided makefile for specific compiler option of your choice
-----------------------------------------------------------------------
Compilation: use the provided makefile with the following options:
> make            ! compiles with -O3 optimization on !
> make DEBUG=TRUE ! compiles with check and warning flags on !
> make OMP=TRUE   ! compiles with -openmp (OpenMP) option on !

-----------------------------------------------------------------------
Example (test) directory:
../ALADYN.test

Running a tets case:
run aladyn.bat script in ALADYN.test directory:
> aladyn.bat   ! executes 10 MD steps !
> aladyn.bat -n 100 -m 10 -v 0 ! executes 100 MD steps,      !
                               ! reporting every 10th step,  !
                               ! using version 0 subroutines !

-----------------------------------------------------------------------
Required input files:

ann.dat - Neural network potential file
structure.plt - input atomic structure file

--- Available test structures ---
in STR directory:
Al_N500_T100K.plt - 500 atoms Al crystal at 100K
Al_N4500.plt      - 4500 atoms Al crystal at 0K
Al_N72000.plt     - 72000 atoms Al crystal at 0K

Use any of the above structures by linking them to structure.plt, e.g.,
ln -s ./STR/Al_N500_T100K.plt structure.plt

-----------------------------------------------------------------------
Execution:

cd ../ALADYN.test
./aladyn

Execution options: (see also ./CM_MINI1.test/aladyn.bat script)

./aladyn             # run, using default options - see below
./aladyn -n 10       # do 10 MD steps (default: -n 10)
./aladyn -n 10 -m 2  # do 10 MD steps, reproting at each 2nd step
                     # (default: -m 1)
./aladyn -v 0        # run version 0 (default: -v 0)
                     # gives the possibilityto test different versions
                     # of the code swithching to different subroutines,
                     # (default: -v 0)
-----------------------------------------------------------------------

Source Files:
   aladyn.f       - Main program
     contains:
      subroutine read_pot  ! reads ann.dat potential file
      subroutine init_param  ! simulation initialization
      subroutine node_config ! cpu node configuration
      subroutine link_cell_setup  ! set-up of the link-cell list
      subroutine get_neighbors_0  ! finds cut-off neighbors (ver. 0)
      subroutine get_nbrs_check   ! get neighbors with overlap check
      subroutine get_nbrs_no_check ! get neighbors with no overlap check
      subroutine SIM_run     ! executes a simulation run !
      subroutine force_calc  ! calls force_ver_#  (# = 0 currently)
      subroutine force_ver_0 ! energy and force calculation (ver. 0)
      subroutine write_structure ! writes an output structure file

   aladyn_sys.f, aladyn_sys_OMP.f, aladyn_sys_NO_OMP.f - system modules
   aladyn_mods.f  - general purpose modules
   aladyn_IO.f    - I/O operations module

   aladyn_ANN.f   - ANN potential energy and force calculation module
     contains:
      subroutine init_param_ANN ! initializes the ANN pot. parameters
      subroutine Energy_ANN_0   ! potential energy calculation (ver. 0)
      subroutine force_ANN_0    ! force calculation (ver. 0)

   aladyn_MD.f    - molecular dynamics module
     contains:
      subroutine init_MD   ! MD run initialization
      subroutine init_vel  ! velocity initialization
      subroutine initaccel ! acceleration initialization (based on pot)
      subroutine get_T     ! Calculates current system temperature
      subroutine predict_atoms ! Gear predictor call !
      subroutine correct_atoms ! Gear corrector call !

   aladyn_MSR.f   - measure subroutines module
     contains:
      subroutine measure_energy ! energy measurement !
      subroutine report         ! writes a report line in the output !

-----------------------------------------------------------------------
Subroutines for optimization are those wich end with _0 (version 0):
They are called from force_calc in aladyn.f, depending on -v parameter:

       select case(iver)
        case(0)
         call force_ver_0(ichk,iforce) ! current default version !
        case(1)
         call force_ver_1(ichk,iforce) ! to be made by users !
        ...

        end select

Subroutine force_ver_0() calls _0 versions of the respective subroutines:
 specifically:
  call get_neighbors_0(ichk)
  call Energy_ANN_0(natoms)
  call force_ANN_0(1,natoms)

Alternatively, force_ver_1() should call the _1 versions of those
subroutines, when prepared, like:
  call get_neighbors_1(ichk)
  call Energy_ANN_1(natoms)
  call force_ANN_1(1,natoms)

=======================================================================
 Notices:
 Copyright 2018 United States Government as represented by the
 Administrator of the National Aeronautics and Space Administration.
 All Rights Reserved.

 Disclaimers:
 No Warranty: THE SUBJECT SOFTWARE IS PROVIDED "AS IS" WITHOUT ANY
 WARRANTY OF ANY KIND, EITHER EXPRESSED, IMPLIED, OR STATUTORY,
 INCLUDING, BUT NOT LIMITED TO, ANY WARRANTY THAT THE SUBJECT SOFTWARE
 WILL CONFORM TO SPECIFICATIONS, ANY IMPLIED WARRANTIES OF
 MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, OR FREEDOM FROM
 INFRINGEMENT, ANY WARRANTY THAT THE SUBJECT SOFTWARE WILL BE ERROR
 FREE, OR ANY WARRANTY THAT DOCUMENTATION, IF PROVIDED, WILL CONFORM
 TO THE SUBJECT SOFTWARE. THIS AGREEMENT DOES NOT,IN ANY MANNER,
 CONSTITUTE AN ENDORSEMENT BY GOVERNMENT AGENCY OR ANY PRIOR RECIPIENT
 OF ANY RESULTS, RESULTING DESIGNS, HARDWARE, SOFTWARE PRODUCTS
 OR ANY OTHER APPLICATIONS RESULTING FROM USE OF THE SUBJECT SOFTWARE.
 FURTHER, GOVERNMENT AGENCY DISCLAIMS ALL WARRANTIES AND LIABILITIES
 REGARDING THIRD-PARTY SOFTWARE, IF PRESENT IN THE ORIGINAL SOFTWARE,
 AND DISTRIBUTES IT "AS IS." 

 Waiver and Indemnity:
 RECIPIENT AGREES TO WAIVE ANY AND ALL CLAIMS AGAINST THE UNITED STATES
 GOVERNMENT, ITS CONTRACTORS AND SUBCONTRACTORS, AS WELL AS ANY PRIOR
 RECIPIENT.  IF RECIPIENT'S USE OF THE SUBJECT SOFTWARE RESULTS IN ANY
 LIABILITIES, DEMANDS, DAMAGES, EXPENSES OR LOSSES ARISING FROM SUCH
 USE, INCLUDING ANY DAMAGES FROM PRODUCTS BASED ON, OR RESULTING FROM,
 RECIPIENT'S USE OF THE SUBJECT SOFTWARE, RECIPIENT SHALL INDEMNIFY AND
 HOLD HARMLESS THE UNITED STATES GOVERNMENT, ITS CONTRACTORS AND
 SUBCONTRACTORS, AS WELL AS ANY PRIOR RECIPIENT, TO THE EXTENT
 PERMITTED BY LAW. RECIPIENT'S SOLE REMEDY FOR ANY SUCH MATTER SHALL
 BE THE IMMEDIATE, UNILATERAL TERMINATION OF THIS AGREEMENT.

