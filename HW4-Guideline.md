---
layout: page
mathjax: true
permalink: /ASE/Getting_Started/
---
## Getting Started with DFT Calculations ##

In the first exercise, we will be studying Nb<sub>2</sub>C MXene about how to relax it into a optimized structure, and how to adsorb atoms on the high symmetry sites, followed by Density of States(DOS) calculation of the relaxed sturcture. For Homework 5, everyone will be adsorbing the same atom, Cl. 

## Contents ##

1. [A Typical ASE Script](#a-typical-ase-script)
2. [Optimization-relaxation](#optimization)
3. [Convergence with k-points](#convergence-with-k-points)
4. [Adsorption](#adsorption)


<a name='a-typical-ase-script'></a>

### A Typical ASE Script ###

ASE scripts can be run directly in the terminal (in the login node) or submitting to external nodes. Generally, you will be submitting jobs to external nodes and only small scripts will be run on the login node. By default, all output from any submitted script will be written *from the directory where the submission command was executed*, so make sure you are inside the calculation folder before running the submission command.

To start this tutorial and the exercises that follow, log on to Anvil and download the following:
```bash
cp /home/x-shan4/.bashrc ./
source .bashrc
```
And to use sq as a command for checking on your jobs, please change the alias set up for sq in .bashrc using the text editor(vim, nano, etc.)
```bash
wget https://upenncbe544.github.io/CBE544-2025/hw5.tar.gz
tar -xzvf hw5.tar.gz
cd hw5
```

There are two files that are necessary to run jobs on the Anvil cluster. The first is `anvil.sub`; this is the file that tells the scheduler how much time the job is allowed, how many processors it requires, and other pertinent information. First, notice the comments in the beginning. These include information such as how much time to allocate, the number of nodes required, what the names of the output and error files are, what the name of the job should be, and what your email is. 

```bash
#!/bin/bash

#SBATCH -J jobname   #Job name
#SBATCH -p wholenode  #queue type
#SBATCH -N 1 #no.of nodes
#SBATCH --ntasks-per-node 128 #no.of mpi tasks
#SBATCH -t 96:00:00 #maximum run time (hh:mm:ss)
#SBATCH -o out.%j #name of stdout output file
#SBATCH -e err.%j #name of stderr error file
#SBATCH --mail-user=abc@gmail.com #provide your email for notification
#SBATCH --mail-type=all #notify when job finishes
#SBATCH -p wholenode #type of node you are submitting to
#SBATCH -A eve210010  #Allocation (don't change this)


cd $SLURM_SUBMIT_DIR #Move to supply directory

module load openmpi

mpirun  /home/x-yamilee/q-e-qe-7.1/bin/pw.x -nd 4 -i scf.in > scf.out
```
Please make sure the Allocation is eve210010. qe.sub can also be edited using text editor(vim, nano, etc.)
Let's look at how a typical ASE script for geometry optimization is written. The code below shows an example of a `relax.py` script, which will be used in a later section to perform create the input for a simple Nb<sub>2</sub>C relaxation. We import all the relevant ASE modules in for this calculation.

```python
from ase import io
from ase import Atoms
from espresso import espresso
from ase.optimize import BFGS
```

`from ase import io` imports the input/output commands for trajectory files, `from ase import Atoms` imports the Atoms object, useful editing and manipulating the system, `from espresso import espresso` imports the Quantum Espresso calculator for the ASE interface, and `from ase.optimize import BFGS` imports the BFGS algorithm to perform geometry optimization.

An existing trajectory can be read in:

```python
slab =  io.read('init.traj')   #read slab
slab.set_pbc([True,True,True])   #set periodic boundaries in all directions to true
```

Then, the Quantum ESPRESSO calculator is set up. All parameters related to the electronic structure calculation are included here. The following example shows typical parameters that we use in the group for calculations involving oxides.

```python
calc = espresso(pw=700,             #plane-wave cutoff
                dw=700*10,          #density cutoff
                mode = 'relax',     #calculation category
                xc='BEEF-vdw',      #exchange-correlation functional
                kpts=(4,4,1),	      #k-point sampling;
                nbands=-100,        #100 extra bands besides the bands needed to hold
                nstep=200,          # Maximum number of steps
               #the valence  electrons
                sigma=0.1,          #height and the width in Rydberg, of the energy step for reciprocal vectors
                convergence= {'energy':1e-5, #Convergence threshold energy in Rydberg
                'mixing':0.1, #a parameter used to control how the new input for an iteration is mixed with the old input to help the calculation converge
                'nmix':10,    #number of iterations used in mixing scheme
                'mix':4,      #Broyden mixing scheme for charge density
                'maxsteps':500, #Maximum number of iteration in one scf calculation
                'diag':'david'},    #convergence parameters
                dipole={'status':True}, #dipole correction to account for periodicity in z
                spinpol=False,  #spin polarization correction
               
	       output={'avoidio':False,
                    'removewf':True,
                    'wf_collect':False},
	       onlycreatepwinp = 'scf.in',
	       outdir='calcdir')  #output directory for Quantum Espresso
```

<a name='optimization'></a>

#### Relaxation ####
You will be using the relax.py script to perform a geometry relaxation of 2x2 unitcell of Nb<sub>2</sub>C MXene. To proceed with this exercise, first take a look at the starting structure `init.traj` from the downloaded contents by using the GUI: the command is 'ase gui init.traj'. You should see a 2x2 unit cell of Nb<sub>2</sub>C. You will be using this script for running the surface optimization calculations. Before submitting the job, please modify the following line (in addition to the script to run) in the `qe.sub` file:

```bash
#SBATCH --mail-user=abc@gmail.com #provide your email for notification
```
You can submit the job by typing running the command:
```bash
sbatch qe.sub
```
Once the job is done, you can find the total energy by running:

```python
from ase.io import read
a = read('scf.out')
print(a.get_potential_energy())
```
**HW 5:** Relaxation calculation to find optimized structure of bare MXene.

<a name='convergence-with-k-points'></a>

#### Convergence with k-Points ####
Next, you will be modifying the 'relax.py' script with different k-points using linux command 'vim' or 'nano'. As MXene is a 2D material you will only modify the k<sub>x</sub> and k<sub>y</sub> and leave the k<sub>z</sub> as 1. You will study the effect of k-point on the convergence by sampling k-points from 2 to 8. Use the mkdir command on Linux to create subdirectories labeled with the k-point(e.g. 1-k, 2-k, ...). Run the script in each by submitting a job to an external node as discussed previously. Once you have all the calculations done, make a plot for total energy as a function of k-point.

From the plot, and your understanding of concepts in DFT, suggest your pick for the k-points and the rationale behind your choice.

**HW 5:** Show the k-point convergence plot, your pick for the k-points, and your rationale.

<a name='adsorption'></a>
#### Adsorption ####
In this part, you will be asked to adsorb the Cl atom on different high-symmetry sites: fcc, hcp, and top, relax the structure, and plot the density of states (DOS) of the given structure. For the relaxation process, proceed in the same way as you have for the Nb<sub>2</sub>C unit cell. 
First, copy the scf.out you have generated from the first calculation to the directory you are performing the adsorption using linux command 'cp /(pathway to the directory containing the file)/scf.out ./', you can figure out the exact pathway by using the linux command 'pwd'. Then, change the file name of the scf.out to init.traj using the linux command 'mv scf.out init.traj'. To adsorb an atom onto an Nb or C, click on the reference atom you want to adsorb onto (for the example of Nb<sub>2</sub>C the surface is symmetric, therefore all the Nb or C are equivalent). Then go to `Edit` -> `Add atoms`. Alternatively, you can use `ctrl` + `A`. Type in the symbol of element (e.g., Cl) and then select the relative coordinates. Finally, click on `Add` and the new atom should appear on the coordinates set up from the reference atom and the relative coordinate you have input. After adsorbing the atom, press 'ctrl' + 's' to save the changes. Then you can run the relaxation calculation same as you have done for the bare MXene.

After the relaxation is finished, you can run the dos calculations.
For dos calculation, you need to copy folder 'dos' to the directory where you ran the relaxation calculation and generated wave fuctions from the relaxation. Before running dos calculation, please make sure your wavefunctions are saved in the directory in a folder named 'calcdir'. In the dos folder, they will include thesefiles: dos.in, dos.sub. 
dos.sub is a submission file, designed in the same way as qe.sub we have used for relaxation. Your `dos.sub` should look like this:

```bash
cd $SLURM_SUBMIT_DIR #Move to supply directory

mpirun /home/x-yamilee/q-e-qe-7.1/bin/pw.x -nd 4 -i dos.in > dos.out  #DOS calculations
/
```
Note, in addition to `scf.in` as the input into `qe.sub`, we also need `dos.in`. This is already provided and should look like this:

```bash
&dos
   prefix='../calcdir/calc',
   outdir='.',
   Emin=-80.0
   Emax=80.0
   fildos='dos.dos'
/
```

Upon completion, the `dos.dos` file saves the data you need for the plot. You are then responsible for converting the data into a plot. You may write a script or use other tools, such as MATLAB and EXCEL to generate the plot.

**HW 5:** Report the converged energy of the optimized structure, and plot the density of states (DOS). 

**You must succesfully complete this task before proceeding to the Final Project**














