# THE 1H-MR SPECTROSCOPY GLIAL MARKER MYO-INOSITOL MEASURED AT 7T ACROSS THE ALZHEIMER’S DISEASE SPECTRUM
Laura Göschel, Ariane Fillmer, Andrea Dell’Orco, Jeanette Melin, Semiha Aydin, Lea Kurz, Hui Wang, Bernd Ittermann, Dan Rujescu, Leslie Pendrill, Theresa Köbe, Agnes Flöel, 2021

This repository contains part of the code which was used in the given paper. Code for MRS (pre-)processing is not included. 

1. Structural Magnetic Resonance Imaging
    + Preprocessing using [`SPM12`](https://www.fil.ion.ucl.ac.uk/spm/) (Wellcome Trust Centre for Neuroimaging, Institute of Neurology at University College London, UK) and [`FSL`](https://www.fmrib.ox.ac.uk/fsl) (FMRIB software library, University of Oxford, UK)
    + Segmentation using [`FreeSurfer 7.1`](https://surfer.nmr.mgh.harvard.edu/)

2. Resting state functional Magnetic Resonance Imaging [(go to directory)](https://github.com/NeuroMET/Code_for_Paper_Myo-inositol_Goschel2021/tree/main/notebooks)
    + Preprocessing using the standard pipeline from [`fMRIprep v20.1.1`](http://fmriprep.readthedocs.io) 
    + Smoothing, standard denoising and band pass filtering using [`CONN Toolbox v20b`](www.nitrc.org/projects/conn)(RRID:SCR_009550)
    + Creation of the seed and normalization to standard space using [`ANTsPy`](https://antspy.readthedocs.io/en/latest/) [(go to Notebook)](https://github.com/NeuroMET/Code_for_Paper_Myo-inositol_Goschel2021/blob/main/notebooks/Seed_from_MRSvoxels)
    + Creation of connectivity maps using [`CONN Toolbox v20b`](www.nitrc.org/projects/conn)
    + Creation of binary mask (threshold > 0.3 in cognitively healthy) and extraction of seed-based connectivity [(go to Notebook)](https://github.com/NeuroMET/Code_for_Paper_Myo-inositol_Goschel2021/blob/main/notebooks/Seed-based_connectivities.ipynb)

3. Statistical analyses using [`RStudio`](http://www.rstudio.com/) (RStudio Team (2020). RStudio: Integrated Development for R. RStudio, Inc., Boston, MA)
