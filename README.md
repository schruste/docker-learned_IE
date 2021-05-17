# docker-learned_IE
This is the github repository used to generate the docker image for the paper "Learned infinite elements: Hohage, Lehrenfeld, Preuss". 
The same document ist available [here](https://gitlab.gwdg.de/learned_infinite_elements/learned_ie/-/tree/master/reproduction). 

# How to run / install

We offer a couple of interactive notebooks which can be run live in the browser without the need to install anything:

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/git/https%3A%2F%2Fgitlab.gwdg.de%2Flearned_infinite_elements%2Flearned_ie/master?filepath=notebooks%2Foverview.ipynb) (jupyter notebook)

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/git/https%3A%2F%2Fgitlab.gwdg.de%2Flearned_infinite_elements%2Flearned_ie/master?urlpath=lab) (jupyter lab (no ngsolve webgui))
 
We describe two options to setup the software for running the experiments. 

* downloading a `docker image` from `GRO.data` or `Docker Hub` which contains all dependencies and tools to run the application,
* or installing everything manually on your own local machine. 

The first option is quick and convenient while the second option provides higher flexibility. 
Therefore, we recommend to start with the `docker image`. If you would like to integrate 
parts of the application into your own code (e.g. coupling the optimization routine for dtn 
to another FEM library) then eventually you need to go for a manual installation. Please 
contact <j.preuss@math.uni-goettingen.de> in case of problems. 

## Pulling the docker image from Docker Hub 
* Please install the `docker` platform for your distribution as described [here](https://docs.docker.com/get-docker/).
* After installation the `Docker daemon` has to be started. This can either be done on boot or manually. In most Linux 
distribution the command for the latter is either `systemctl start docker` or `service docker start`.
* Pull the docker image for learned IEs using the command `docker pull schruste/learned_ie:20210517`. 
* Run the image with `docker run -it schruste/learned_ie:20210517 bash`. 
* Proceed further as described in [How to reproduce](#repro).

## Downloading the docker image from GRO.data 
* For this option the first two steps are the same as above. 
* Assuming that `learnedIE_reproduction.tar` is the filename of the downloaded image, please load the image with `docker load < learnedIE_reproduction.tar`.
* Run the image with `docker run -it schruste/learned_ie:20210517 bash`.
* Proceed further as described in [How to reproduce](#repro).

## Manual installation 

For a local installation it in principle suffices to execute the same commands that are described in the [Dockerfile](https://github.com/schruste/docker-learned_IE/blob/master/Dockerfile)
for assembling the `docker image`. For convenience an overview of the main components is given here. 
Firstly, we need some basic tools. Probably most of these (except for *mpmath*) are already installed on your system.

* [Python3](https://www.python.org/) 
  * [NumPy/SciPy](https://www.scipy.org/install.html)
  * [mpmath](http://mpmath.org/): This is a multi-precision library which we use for evaluation of special functions. 
* [cmake](https://cmake.org/)

We proceed to the more specific software. The components *ngs_refsol*,*ceres_dtn* and *pole_finder* described below
are submodules of the gitlab repository [learned_IE](https://gitlab.gwdg.de/learned_infinite_elements/learned_ie), which can be obtained by

```git clone https://gitlab.gwdg.de/learned_infinite_elements/learned_ie.git```. 

The main tools are:

* [Netgen/NGSove](https://ngsolve.org/) is the FEM library used for implementation in the paper (if you are only interested in the optimization routine for dtn this 
  does not have to be installed). Installation instructions for common operating systems are provided [here](https://ngsolve.org/downloads).
    * *ngs_refsol*: Minor extension of *NGSolve* that provides some sample solutions for scattering problems. For installation it should be sufficient to 
      execute `python3 setup.py install --user` in the top folder of this repository. Alternatively, one may proceed by a manual installation: 
      
      ```bash
      mdkir build 
      cd build 
      cmake ..
      ```  
* [Ceres Solver](http://ceres-solver.org) Nonlinear least squares solver utilized for solving the optimization problem for dtn. Installation instructions 
  are provided [here](http://ceres-solver.org/installation.html). Dependencies: [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page) and [cmake](https://cmake.org/).
    * *ceres_dtn*: Solves the optimization problem for learned IEs using *Ceres Solver*. Can be installed analogously to *ngs_refsol*.

* *pole_finder*: Routine for computing poles and roots of meromorphic functions. The implementation is in *Python* and independent of the previous two components (but requires 
  *NumPy/SciPy*). Can be installed with `python3 setup.py install --user`. 

# <a name="repro"></a> How to reproduce 

Please switch into the folder `reproduction`.
The scripts for reproducing the experiments are located in subfolders of the folder `scripts`. The subfolders 
are labelled according to the corresponding sections of the paper. The scripts are run with the command 
`python3 scriptname.py `. While running the scripts the data for reproducing the results of the paper will usually be written to 
specific files (located in the folder in which the script is run) which are explained below for each experiment. For comparison, the folder `data` contains the data 
which has been used in the paper.

## 4.3 Diagonalization and pole structure 

Change to the directory `scripts/4_3_diag_poles`. Run `python3 learning_comp.py`. 

### Fig. 3 (a)
The data is stored in the created files *ansatz-residuals-N__k__.dat*, where the natural number __k__ describes the degrees of freedom of the learned IEs in radial direction. 
The table in these files contains the following columns: 

* *l*: The variable l describing the mode number (x-axis in Fig. 3(a)).
* The next two columns give the relative error at mode number *l* for the different ansatzes. The column *minimalIC* contains the results 
for the reduced ansatz while the column *full* the results for the dense ansatz. 

### Fig. 3 (b)
The data for this plot is output to the terminal while running *learning_comp.py*. 
The poles for the learned IEs with *N* degrees of freedom are indicated by the output *Poles for learned infinite elements*. 
The analytic poles follow after the output *analytic poles*. 

## 6.1 Scattering of a plane wave from a disk

Change to the directory `scripts/6_1_plane_wave`.

### Fig. 5
* Run the experiment using `python3 plane_wave.py`. Afterwards the data for Fig. 5 (a) will be available in the file *plane-wave-k16.dat*. 
The first column `N` is the number of infinite element degrees of freedom in radial direction. The next columns give the polynomial degree of 
the finite elements, so *p__j__* means order=__j__. The table contains the relative L^2-error on the computational domain. 
* The file *plane-wave-p10.dat* contains the data for Fig. 5 (b). The columns are self-explanatorily labelled according to the different wavenumbers.
* The data for the plot of the reference solution shown in the inset is available in the file `plane-wave-k16.vtk`. 

### Fig. 6
* Run the experiment using `python3 plane_wave_decrease_coupling_radius.py`. 
* The data for Fig. 6 (a) is avalaible in *plane-wave-a-relerror.dat*. The columes are labelled according to the 
position of the coupling boundary. E.g. the last column, *a1.0* means a = 1.0 and so a-R = 1/2 (for R = 1/2 as in the paper).
* The data for Fig. 6 (b) can be found in *plane-wave-a-uniform-err.dat* with the same labelling of columns as above. 

## 6.2 Point source inside unit disk (Fig. 7)

Change to the directory `scripts/6_2_pt_source`. For each of the compared methods (learned IE, HSIE, adaptive PML and TP-PML)
there is a specific file that has to be run to reproduce the results for the method in Fig. 7 (a-d). 
Variables or functions that are shared between different methods are contained in the file *pt\_source\_shared.py*, for example 
the wavenumber or the source position. This file should only be edited if the user wishes to choose different parameters than
for the experiment presented in the paper.

* Learned IE: The results for learned infinite elements are reproduced running `python3 pt-source-learnedIE.py`. To switch between the reduced 
and dense ansatz for the learned infinite element matrices the file has to be edited. In the beginning of the file there is a block of code 
marked as to be set by the user. To use the reduced ansatz set ansatz = "minimalIC". For the dense ansatz set ansatz = "full". After running the script
the results will be available in the file *pt-source-learned-ansatz.dat* where `ansatz` is "minimalIC" or "full". The first column of the table *N* 
is the number of infinite element degrees of freedom. The columns *ndofs* and *nzes*  give the number of degrees of freedom and number 
of nonzero entries of the resulting linear system respectively. The relative L^2-error on the coupling boundary for the two different source 
positions is given in the last two columns. The case *close* means that the source is close to the boundary, i.e. at x = 0.95 while *far* 
means that it is far away at x=0.5 (the labels for the source positions are defined in the dictionary `problem_data` in *pt\_source\_shared.py*).

* HSIE: Run `python3 pt-source-HSIE.py`. Afterwards the results are available in the file *pt-source-HSIE.dat*. The columns are labelled
in the same way as above for the learned IE.

* TP-PML: Run `python3 ptsource-TP-pml.py`. Afterwards the results are available in the file *pt-source-TP-pml.dat*. The table is arranged as for the case of learned IE and HSIE except that the column *N* is missing. 

* adaptive PML: Run `python3 pt-source-adaptive-pml.py`. This time a separate file for each source position is created: `pt-source-adaptive-PML-far.dat` for source at x=0.5 and `pt-source-adaptive-PML-close.dat` for source at x=0.95. The first two columns are number of degrees of freedom (*ndofs*) and number of nonzero entries (*nzes*) of the linear system. The last column (*relerr*) is the relative error on the coupling boundary.

## 6.3 Jump in exterior wave speed (Fig. 1(a,b) and Fig. 8)

Change to the directory `scripts/6_3_jump_wavespeed`. Run `python3 jump.py`. Several files called *jump-dtn-kinf__x__-__y__.dat* are created, where 

* The variavle __x__ denote the value of the wavenumber k infinity, e.g. __x__ = __8__.
* The variable __y__ describes if the file contains the results for the __real__ or the __imaginary__ part of the dtn approximation. In the main paper only the real part is considered.

The tables in all the files follow the same structure: 

* The first column *N* denotes the number of infinite element degrees of freedom. 
* The second column *zeta* gives the values of the real or imaginary part of the reference dtn function. 
  This data has been used to generate the plots Fig.1(a) for __x__ = __16__ and Fig.1(b) for __x__ = __8__.
* The next columns labelled as *N__x__* give the approximation with the learned infinite elements with __x__ degrees of freedom.

The analytic (Fig. 1(a,b)) and learned poles (Fig.1(a,b) and Fig.8) of dtn are output to the terminal while running the script. 

The relative L^2-eror for the scattering of a plane wave is available in the file *jump-l2_relerr.dat*. The first column `N` is as above. The other columns labelled as `kInf__x__` contain the results for the respective wavenumbers k infinity, e.g. __x__  = __8__. Moreover, the data for creating the plot of the reference solution shown in the inset of Fig. 8 c) is available in *jump-sol-k_inf8.vtk*. 

## 6.4 Waveguide (Fig.1 (c ) and Fig. 9) 

Change to the directory `scripts/6_4_waveguide`. Run the file `python3 waveguide.py`.

* Fig.1(c ): The real and imaginary part for dtn are available in the files *waveguide-dtn-k16.5-real.dat* and *waveguide-dtn-k16.5-imag.dat* respectively. 
  The first column `lam` contains the sample points and the second column the values of dtn. 
* Fig.9(a): The dtn approximation with learned IEs is also contained in the file `waveguide-dtn-k16.5-imag.dat`. 
  Column *N__j__* contain the results for __j__ infinite elements degrees of freedom. The values of the reference dtn at the eigenvalues (black crosses) 
  are given in the file *waveguide-dtn-k16.5-interp.dat*. The second column contains the values of the real part and the third the values for the imaginary part.
  The location of the learned poles is output to the terminal while running the script. 
* Fig.9(b): The file `waveguide-k16.5.dat` contains the relative L^2-error. The first column gives the number `N` of infinite element dofs.
  The next columns give the results for different polynomial degrees of the finite elements, so *p__j__* means order=__j__. The file 
  *waveguide-refsol-k16.5.vtk* contains the data for the plot of the sample solution shown in the inset.  

## 6.5 VAL-C model (Fig.1(d), Fig.2, Fig. 10 and Table 1) 

Change to the directory `scripts/6_5_VALC`. 

* The data for Fig.2 (except the inset of (b)) is obtained by running `python3 VALC-coeff-plot.py`. Two files will be produced. 
The file `c-rho-VALC.dat` contains the data for sound speed and density of the VAL-C model in the columns `cV` and `rhoV` respectively at radial coordinate `r` (first column).
The columns `cA` and `rhoA` correspond to a sound speed and density of a simplified atmospheric model shown as dashed lines in Figure 2 (a).
The data for the effective potential is available in the file *eff-pot-VALC.dat*. The second column contains the real part and the third the imaginary part. 
* Running `python3 learn-helio.py` yields the data for Fig. 1(d), Fig.10 and the inset of Fig.2 (b).
    * Fig.1(d): The data for the  reference dtn function is available in the file *dtn-VALC-ref-0.007Hz.dat*.
      The first column contains the mode number *l* the second *zetaReal* the real part of dtn and the third the imaginary part.
      The poles of dtn are output to the terminal indicated by the text `poles of dtn function`. 
    * Fig 10: The data for the reference dtn function is the same as for Fig.1 (d). The file *learned-dtn-VALC-real.dat* contains the dtn approximation with the learned IEs for 
      the real part shown in Fig. 10(a). The columns *N__k__* contain the results for __k__ infinite elements degrees of freedom. The approximation of the imaginary part 
      with learned IEs is available in the file *learned-dtn-VALC-imag.dat*. The poles of the dtn function (Fig. 1(d)) are output to the terminal.
      Additionally, the learned poles (not shown in the paper) are printed as well. 
    * Fig. 2(b): The data for the real part of the modes shown in the inset is available in the file *VALC-modes-0.007Hz.dat*. The columns are labelled according to the number 
      number, so `l4000` contains the values for mode with number `l=4000`. 
* The values in Table 1 are output to the terminal while running `python3 integrate_wavelength.py`.
