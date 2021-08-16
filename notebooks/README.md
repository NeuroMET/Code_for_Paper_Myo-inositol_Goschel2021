# 1. (Pre-)processing of structural MRI data

## 1.1 Preprocessing using SPM12 and FSL
The performed MP2Rage sequence results in various output images. The T1wUNI image provides optimal contrast in brain tissue, but its high background noise, caused by the high (7 T) magnetic field, causes trouble to widely used software packages. The denoised image (T1wDEN), on the other hand, provides a clean background but worse contrast in the brain area. Therefore, Statistical Parametric Mapping software 12 ([`SPM12`](https://www.fil.ion.ucl.ac.uk/spm/), Wellcome Trust Centre for Neuroimaging, Institute of Neurology at University College London, UK) was used for bias field correction and segmentation of the T1wUNI and T1wDEN images in gray matter, white matter and cerebrospinal fluid. Using [`FSL v6.0`](https://www.fmrib.ox.ac.uk/fsl) (FMRIB software library, University of Oxford, UK), these three segments were added up for each T1wUNI and T1wDEN T1w image resulting in two brain masks for each participant. After visual inspection, the individual brain mask, which showed a better fit (mask from either the T1wUNI or the T1wDEN image), was used to compose a final T1w image: The brain tissue was extracted from the bias field corrected T1wUNI image (has better contrast in brain tissue), and the skull and background from the T1wDEN (has better skull stripping). This procedure was necessary to improve the subsequent segmentation.

## 1.2 Segmentation using FreeSurfer 7.1
Segmented brain volumes of cortical and subcortical regions were then estimated by the widely used [`FreeSurfer 7.1`](https://surfer.nmr.mgh.harvard.edu/) image analysis suite (Fischl and Dale, 2000). For improved segmentation performance, the previously generated brain masks were provided to FreeSurfer replacing the brain mask generated during the FreeSufer skull stripping process. 

# 2. (Pre-)processing of resting state functional MRI data

## 2.1 Preprocessing using fMRIprep v20.1.1

Resting state functional MRI data which were acquired using a 7T whole-body Magnetom MRI system (Siemens Healthineers, Erlangen, Germany) were prepocessed using the standard preprocessing pipeline of [*fMRIPrep* 20.1.1](http://fmriprep.readthedocs.io) (@fmriprep1; @fmriprep2; RRID:SCR_016216), which is based on *Nipype* 1.5.0 (@nipype1; @nipype2; RRID:SCR_002502). Briefly, the preprocessing pipeline included slice-time correction, distortion correction with co-registered fieldmap images and co-registration to individual bias field corrected T1w image and subsequent normalization to standard space (MNI152NLin2009cAsym). Several confounding parameters were estimated, i.e. CSF, WM and global signal, as well as physiological, anatomical, temporal nuisance components. The following detailed description of preprocessing steps was generated automatically by fMRIprep:

### Preprocessing of anatomical data 

: A total of 1 T1-weighted (T1w) images were found within the input
BIDS dataset.The T1-weighted (T1w) image was corrected for intensity non-uniformity (INU)
with `N4BiasFieldCorrection` [@n4], distributed with ANTs 2.2.0 [@ants, RRID:SCR_004757], and used as T1w-reference throughout the workflow.
The T1w-reference was then skull-stripped with a *Nipype* implementation of
the `antsBrainExtraction.sh` workflow (from ANTs), using OASIS30ANTs
as target template.
Brain tissue segmentation of cerebrospinal fluid (CSF),
white-matter (WM) and gray-matter (GM) was performed on
the brain-extracted T1w using `fast` [FSL 5.0.9, RRID:SCR_002823,
@fsl_fast].
Volume-based spatial normalization to one standard space (MNI152NLin2009cAsym) was performed through
nonlinear registration with `antsRegistration` (ANTs 2.2.0),
using brain-extracted versions of both T1w reference and the T1w template.
The following template was selected for spatial normalization:
*ICBM 152 Nonlinear Asymmetrical template version 2009c* [@mni152nlin2009casym, RRID:SCR_008796; TemplateFlow ID: MNI152NLin2009cAsym], 

### Preprocessing of functional data

: For each of the 1 BOLD runs found per subject (across all
tasks and sessions), the following preprocessing was performed.
First, a reference volume and its skull-stripped version were generated
using a custom methodology of *fMRIPrep*.
Head-motion parameters with respect to the BOLD reference
(transformation matrices, and six corresponding rotation and translation
parameters) are estimated before any spatiotemporal filtering using
`mcflirt` [FSL 5.0.9, @mcflirt].
BOLD runs were slice-time corrected using `3dTshift` from
AFNI 20160207 [@afni, RRID:SCR_005927].
A B0-nonuniformity map (or *fieldmap*) was estimated based on a phase-difference map
calculated with a dual-echo GRE (gradient-recall echo) sequence, processed with a
custom workflow of *SDCFlows* inspired by the
[`epidewarp.fsl` script](http://www.nmr.mgh.harvard.edu/~greve/fbirn/b0/epidewarp.fsl)
and further improvements in HCP Pipelines [@hcppipelines].
The *fieldmap* was then co-registered to the target EPI (echo-planar imaging)
reference run and converted to a displacements field map (amenable to registration
tools such as ANTs) with FSL's `fugue` and other *SDCflows* tools.
Based on the estimated susceptibility distortion, a corrected
EPI (echo-planar imaging) reference was calculated for a more
accurate co-registration with the anatomical reference.
The BOLD reference was then co-registered to the T1w reference using
`flirt` [FSL 5.0.9, @flirt] with the boundary-based registration [@bbr]
cost-function.
Co-registration was configured with nine degrees of freedom to account
for distortions remaining in the BOLD reference.
The BOLD time-series (including slice-timing correction when applied)
were resampled onto their original, native space by applying
a single, composite transform to correct for head-motion and
susceptibility distortions.
These resampled BOLD time-series will be referred to as *preprocessed
BOLD in original space*, or just *preprocessed BOLD*.
The BOLD time-series were resampled into standard space,
generating a *preprocessed BOLD run in MNI152NLin2009cAsym space*.
First, a reference volume and its skull-stripped version were generated
using a custom methodology of *fMRIPrep*.
Several confounding time-series were calculated based on the
*preprocessed BOLD*: framewise displacement (FD), DVARS and
three region-wise global signals.
FD was computed using two formulations following Power (absolute sum of
relative motions, @power_fd_dvars) and Jenkinson (relative root mean square
displacement between affines, @mcflirt).
FD and DVARS are calculated for each functional run, both using their
implementations in *Nipype* [following the definitions by @power_fd_dvars].
The three global signals are extracted within the CSF, the WM, and
the whole-brain masks.
Additionally, a set of physiological regressors were extracted to
allow for component-based noise correction [*CompCor*, @compcor].
Principal components are estimated after high-pass filtering the
*preprocessed BOLD* time-series (using a discrete cosine filter with
128s cut-off) for the two *CompCor* variants: temporal (tCompCor)
and anatomical (aCompCor).
tCompCor components are then calculated from the top 5% variable
voxels within a mask covering the subcortical regions.
This subcortical mask is obtained by heavily eroding the brain mask,
which ensures it does not include cortical GM regions.
For aCompCor, components are calculated within the intersection of
the aforementioned mask and the union of CSF and WM masks calculated
in T1w space, after their projection to the native space of each
functional run (using the inverse BOLD-to-T1w transformation). Components
are also calculated separately within the WM and CSF masks.
For each CompCor decomposition, the *k* components with the largest singular
values are retained, such that the retained components' time series are
sufficient to explain 50 percent of variance across the nuisance mask (CSF,
WM, combined, or temporal). The remaining components are dropped from
consideration.
The head-motion estimates calculated in the correction step were also
placed within the corresponding confounds file.
The confound time series derived from head motion estimates and global
signals were expanded with the inclusion of temporal derivatives and
quadratic terms for each [@confounds_satterthwaite_2013].
Frames that exceeded a threshold of 0.5 mm FD or 1.5 standardised DVARS
were annotated as motion outliers.
All resamplings can be performed with *a single interpolation
step* by composing all the pertinent transformations (i.e. head-motion
transform matrices, susceptibility distortion correction when available,
and co-registrations to anatomical and output spaces).
Gridded (volumetric) resamplings were performed using `antsApplyTransforms` (ANTs),
configured with Lanczos interpolation to minimize the smoothing
effects of other kernels [@lanczos].
Non-gridded (surface) resamplings were performed using `mri_vol2surf`
(FreeSurfer).


Many internal operations of *fMRIPrep* use
*Nilearn* 0.6.2 [@nilearn, RRID:SCR_001362],
mostly within the functional processing workflow.
For more details of the pipeline, see [the section corresponding
to workflows in *fMRIPrep*'s documentation](https://fmriprep.readthedocs.io/en/latest/workflows.html "FMRIPrep's documentation").


### Copyright Waiver

The above boilerplate text was automatically generated by fMRIPrep
with the express intention that users should copy and paste this
text into their manuscripts *unchanged*.
It is released under the [CC0](https://creativecommons.org/publicdomain/zero/1.0/) license.

## 2.2 Denoising using CONN Toolbox v20b

Preprocessed functional data were imported to the [`CONN Toolbox v20b`](www.nitrc.org/projects/conn)(RRID:SCR_009550) and smoothed with a Gaussian kernel of 6mm full width half maximum (FWHM). The nuisance regressors were defined according to the CONN’s default denoising procedure and band-pass filtered at 0.008 Hz – 0.09 Hz.

## 2.3 Creation of the seed using ANTsPy

A seed was created by a sphere of 10mm radius around the individual MRS voxel center coordinates. Using [`ANTsPy`](https://antspy.readthedocs.io/en/latest/) [(go to Notebook)](https://github.com/NeuroMET/Code_for_Paper_Myo-inositol_Goschel2021/blob/main/notebooks/Seed_from_MRSvoxels), this sphere was then normalized to standard space by applying the deformation matrix of the T1w-to-MNI-transformation, which was created during the preprocessing steps.

## 2.4 Creation of connectivity maps using CONN Toolbox v20b


## 2.5 Creation of binary mask and extraction of seed-based connectivity measures for the whole cohort 

The connectivity measures from the seed to the rest of GM voxels of all cognitively healthy subjects was thresholded to 0.3 (corresponding to p < 0.01) and binarized [(go to Notebook)](https://github.com/NeuroMET/Code_for_Paper_Myo-inositol_Goschel2021/blob/main/notebooks/Seed-based_connectivities.ipynb). This resulted in the following mask.

Second, across all diagnostic groups, individual Fisher’s z-transformed seed-based connectivity maps were constructed by applying this binary mask.





### References


Results included in this manuscript come from preprocessing
performed using *fMRIPrep* 20.1.1
(@fmriprep1; @fmriprep2; RRID:SCR_016216),
which is based on *Nipype* 1.5.0
(@nipype1; @nipype2; RRID:SCR_002502).

Anatomical data preprocessing

: A total of 1 T1-weighted (T1w) images were found within the input
BIDS dataset.The T1-weighted (T1w) image was corrected for intensity non-uniformity (INU)
with `N4BiasFieldCorrection` [@n4], distributed with ANTs 2.2.0 [@ants, RRID:SCR_004757], and used as T1w-reference throughout the workflow.
The T1w-reference was then skull-stripped with a *Nipype* implementation of
the `antsBrainExtraction.sh` workflow (from ANTs), using OASIS30ANTs
as target template.
Brain tissue segmentation of cerebrospinal fluid (CSF),
white-matter (WM) and gray-matter (GM) was performed on
the brain-extracted T1w using `fast` [FSL 5.0.9, RRID:SCR_002823,
@fsl_fast].
Volume-based spatial normalization to one standard space (MNI152NLin2009cAsym) was performed through
nonlinear registration with `antsRegistration` (ANTs 2.2.0),
using brain-extracted versions of both T1w reference and the T1w template.
The following template was selected for spatial normalization:
*ICBM 152 Nonlinear Asymmetrical template version 2009c* [@mni152nlin2009casym, RRID:SCR_008796; TemplateFlow ID: MNI152NLin2009cAsym], 

Functional data preprocessing

: For each of the 1 BOLD runs found per subject (across all
tasks and sessions), the following preprocessing was performed.
First, a reference volume and its skull-stripped version were generated
using a custom methodology of *fMRIPrep*.
Head-motion parameters with respect to the BOLD reference
(transformation matrices, and six corresponding rotation and translation
parameters) are estimated before any spatiotemporal filtering using
`mcflirt` [FSL 5.0.9, @mcflirt].
BOLD runs were slice-time corrected using `3dTshift` from
AFNI 20160207 [@afni, RRID:SCR_005927].
Susceptibility distortion correction (SDC) was omitted.
The BOLD reference was then co-registered to the T1w reference using
`flirt` [FSL 5.0.9, @flirt] with the boundary-based registration [@bbr]
cost-function.
Co-registration was configured with nine degrees of freedom to account
for distortions remaining in the BOLD reference.
The BOLD time-series (including slice-timing correction when applied)
were resampled onto their original, native space by applying
the transforms to correct for head-motion.
These resampled BOLD time-series will be referred to as *preprocessed
BOLD in original space*, or just *preprocessed BOLD*.
The BOLD time-series were resampled into standard space,
generating a *preprocessed BOLD run in MNI152NLin2009cAsym space*.
First, a reference volume and its skull-stripped version were generated
using a custom methodology of *fMRIPrep*.
Several confounding time-series were calculated based on the
*preprocessed BOLD*: framewise displacement (FD), DVARS and
three region-wise global signals.
FD was computed using two formulations following Power (absolute sum of
relative motions, @power_fd_dvars) and Jenkinson (relative root mean square
displacement between affines, @mcflirt).
FD and DVARS are calculated for each functional run, both using their
implementations in *Nipype* [following the definitions by @power_fd_dvars].
The three global signals are extracted within the CSF, the WM, and
the whole-brain masks.
Additionally, a set of physiological regressors were extracted to
allow for component-based noise correction [*CompCor*, @compcor].
Principal components are estimated after high-pass filtering the
*preprocessed BOLD* time-series (using a discrete cosine filter with
128s cut-off) for the two *CompCor* variants: temporal (tCompCor)
and anatomical (aCompCor).
tCompCor components are then calculated from the top 5% variable
voxels within a mask covering the subcortical regions.
This subcortical mask is obtained by heavily eroding the brain mask,
which ensures it does not include cortical GM regions.
For aCompCor, components are calculated within the intersection of
the aforementioned mask and the union of CSF and WM masks calculated
in T1w space, after their projection to the native space of each
functional run (using the inverse BOLD-to-T1w transformation). Components
are also calculated separately within the WM and CSF masks.
For each CompCor decomposition, the *k* components with the largest singular
values are retained, such that the retained components' time series are
sufficient to explain 50 percent of variance across the nuisance mask (CSF,
WM, combined, or temporal). The remaining components are dropped from
consideration.
The head-motion estimates calculated in the correction step were also
placed within the corresponding confounds file.
The confound time series derived from head motion estimates and global
signals were expanded with the inclusion of temporal derivatives and
quadratic terms for each [@confounds_satterthwaite_2013].
Frames that exceeded a threshold of 0.5 mm FD or 1.5 standardised DVARS
were annotated as motion outliers.
All resamplings can be performed with *a single interpolation
step* by composing all the pertinent transformations (i.e. head-motion
transform matrices, susceptibility distortion correction when available,
and co-registrations to anatomical and output spaces).
Gridded (volumetric) resamplings were performed using `antsApplyTransforms` (ANTs),
configured with Lanczos interpolation to minimize the smoothing
effects of other kernels [@lanczos].
Non-gridded (surface) resamplings were performed using `mri_vol2surf`
(FreeSurfer).


Many internal operations of *fMRIPrep* use
*Nilearn* 0.6.2 [@nilearn, RRID:SCR_001362],
mostly within the functional processing workflow.
For more details of the pipeline, see [the section corresponding
to workflows in *fMRIPrep*'s documentation](https://fmriprep.readthedocs.io/en/latest/workflows.html "FMRIPrep's documentation").


### Copyright Waiver

The above boilerplate text was automatically generated by fMRIPrep
with the express intention that users should copy and paste this
text into their manuscripts *unchanged*.
It is released under the [CC0](https://creativecommons.org/publicdomain/zero/1.0/) license.

### References
