-----------------------------------------------------------------------
05-28-2019

Version 2.0: OpenMP optomized version for Intel

ALADYN mini-app is a simple molecular dynamics (MD) code for performing 
constant energy atomistic simulations using artificial neural networks 
(ANN) interatomic potential trained for Al crystal structure.
The trained ANN was produced and offered by Ganga P. Pun and Yuri Mishin 
from George Mason University.

=======================================================================

 COMPILATION

-----------------------------------------------------------------------
* FORTRAN compiler (Intel 2003 or newer, or PGI FORTRAN with OpenACC)
-----------------------------------------------------------------------
Source directory: ALADYN.source
-----------------------------------------------------------------------
Compilation: use the provided makefiles with the following options:

Intel compiler:
> make -f makefile.intel            ! compiles with -O3 optimization on 
> make -f makefile.intel DEBUG=TRUE ! check and warning flags on
> make -f makefile.intel OMP=TRUE   ! compile with OpenMP direectives

PGI compiler:
> make -f makefile.pgi              ! compiles with -O3 optimization on 
> make -f makefile.pgi DEBUG=TRUE   ! check and warning flags on
> make -f makefile.pgi OMP=TRUE     ! compile with OpenMP direectives
> make -f makefile.pgi ACC=TRUE  ! compile with OpenMP+OpenACC direectives

Notes: 
1. This release contains two versions of the aladyn_ANN.f file, 
aladyn_ANN_Intrinsic.f and aladyn_ANN_Extrinsic.f, which can be
used intercahgeably thorugh the makefile. The two versions differ only in 
the Frc_ANN_OMP() subroutine. In the Intrinsic version, the 
energy and force calculations are done atom by atom, while in the 
Extrinsic version they are done one operation at a time for all atoms.

Edit the provided makefiles for specific compiler option of your choice
=======================================================================

 EXECUTION

-----------------------------------------------------------------------
Run from the example (test) directory: ALADYN.test

Running a tets case:
> aladyn              ! executes 10 MD steps reporting at each step
> aladyn -n 100 -m 10 ! executes 100 MD steps, reporting every 10-th step

Available PBS scripts used for NASA/LaRC K3 cluster:
ALADYN_intel.job      - run the intel version
ALADYN_pgi_V100.job   - run the pgi version with OpneACC for V100 gpu.

Screen output is riderected to aladyn.out

Example outputs are saved in: aladyn_intel.out and aladyn_pgi.out
=======================================================================

INPUT FILES:

-----------------------------------------------------------------------
ANN.dat - Neural network potential file
structure.plt - input atomic structure file

-----------------------------------------------------------------------
--- Available test structures in directory STR:

Al_N4000.plt      -   4000 atoms Al crystal
Al_N8000.plt      -   8000 atoms Al crystal
Al_N16000.plt     -  16000 atoms Al crystal
Al_N32000.plt     -  32000 atoms Al crystal
Al_N64000.plt     -  64000 atoms Al crystal
Al_N128000.plt    - 128000 atoms Al crystal
Al_N192000.plt    - 192000 atoms Al crystal
Al_N256000.plt    - 256000 atoms Al crystal

Use any of the above structures by linking them to structure.plt, e.g.,
> ln -s STR/Al_N4000.plt structure.plt

=======================================================================

SOURCE FILES:

   aladyn.f       - Main program
   aladyn_sys.f, aladyn_sys_OMP.f, aladyn_sys_NO_OMP.f - system modules
   aladyn_mods.f  - general purpose modules
   aladyn_IO.f    - I/O operations module

   aladyn_ANN.f   - ANN potential energy and force calculation module
    contains:
     subroutine Frc_ANN_OMP  ! force & energy calculation (OpenMP version)
     subroutine Frc_ANN_ACC  ! force & energy calculation (OpenACC version)

   aladyn_MD.f    - molecular dynamics module
     contains:
      subroutine get_T     ! Calculates current system temperature
      subroutine predict_atoms ! Gear predictor call !
      subroutine correct_atoms ! Gear corrector call !

-----------------------------------------------------------------------
Suggested subroutines for optimization: Frc_ANN_OMP and Frc_ANN_ACC


 For further information contact:

 Vesselin Yamakov
 National Institute of Aerospace
 100 Exploration Way,
 Hampton, VA 23666
 phone: (757)-864-2850
 fax:   (757)-864-8911
 e-mail: yamakov@nianet.org



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
=======================================================================


