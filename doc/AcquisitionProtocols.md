# Acquisition protocols for PETSIRD test data

## Main contributors

Kris Thielemans, Nicolas Karakatsanis, Alan Osys,
Pawel Markiewicz, Georg Schramm, Glenn Wells, but based on discussions
in meetings of the [ETSI Consortium](https://etsinitiative.org/etsi-consortium/).

## Aim

This document describes protocols for acquiring and processing of
phantom data that can be used to test if PET list-mode (and associated
calibration) convertors to PETSIRD are correct. This data is *not*
intended for

- QA/QC of your scanner (we need only 1 data-set per
  scanner model/software version).

- checking your reconstruction chain (tests avoid reconstruction
  as much as possible).

The current description is intended to be helpful but not a SOP. Our
intention is to prioritise ease of gathering data and experience,
targeting the [fifth ETSI
hackathon](https://etsinitiative.org/5th-etsi-hackathon-nov-15-16-2026-hybrid/).
A more formal description might follow after the hackathon.

## Description of datasets and tests

Notes:

- All data should be acquired with HFS patient position (this will be
  relaxed later).

- All data should include list-mode data, calibration/normalisation
  files, PET vendor reconstructions and CT (or MR) images. Raw data should be
  in vendor format. All images should be in DICOM format. See the last
  section for directory structure, upload details etc.

- Section titles contain an ALLCAPS name of the dataset (to be used in
  the README)

- Data will need to be open access. We highly recommend to use
  the [Creative Commons Attribution 4.0
  International](https://creativecommons.org/licenses/by/4.0/) license.

### PTSRC: Point source at different radial and axial locations

#### Aims:

- Testing geometric description of the scanner in PETSIRD

- Testing bed movement information

#### Acquisition(s):

- single point source acquisitions with stationary bed:

  - each acquisition should have the point source in a different position

  - at least 3 positions, recommended 4. Approximate positions:
    $(0, -10, L/2 - 10)$, $(10,0,0)$, ideally $(-10, 0, L/3)$,
    and $(10,-Y,0)$, where

    - $L$ is the axial FOV length

    - $Y$ is 120mm (for small FOV scanners, use half the FOV radius)

    - coordinates are given as $(x,y,z)$ in mm with axes
      corresponding to DICOM LPS for a HFS patient
      (see [Coordinate systems — 3D Slicer documentation](https://slicer.readthedocs.io/en/latest/user_guide/coordinate_systems.html#anatomical-coordinate-system))
      and $(0,0,0)$ is in the center of the scanner.

- step-and-shoot bed movement (if supported) for last point source, moving the bed
  in direction of the “head of the patient" (HFS) in at least 2 steps, with total
  horizontal translation minimum $L/5$, maximum $L/3$.

- continuous bed movement (CBM) (if supported) for last point source over
  same range as the step-and-shoot (movement is can be either in reverse direction,
  or restarting from original position, but needs to be documented).
  We suggest a CBM speed that allows to cover the scanner AFOV in 2min, but
  slower is fine.

- Recommended activity \* duration ~ 1 MBq \* 1 min for each bed position (longer
  is fine).

#### Notes:

- Given locations are approximate. There is no need to be accurate in positioning.

- 1 source per acquisition (**not** multiple sources in 1 acquisition).
  This simplifies debugging, voids issues with randoms and background,
  and makes it easier to check activity.

- For each acquisition, a CT (or MR) is required. For acquisitions with
  multiple bed positions, we prefer a single CT (or MR) covering all PET bed positions.

- Na-22 point source or Capillary tube with “bead” of activity are both
  acceptable.

- Avoid issues with scatter and attenuation, so “holder” should be
  minimal.

- Nicolas Karakatsanis has 3d printed custom phantom that can be used
  for positioning (12 cm maximum radial range).

#### How to test:

- (geometric) backprojection, find centre-of-mass, check with known
  location determined from vendor reconstruction.

### RANDOMS: Hot source outside axial FOV

#### Aims:

- Testing randoms information and any algorithms to estimate randoms

#### Acquisition(s):

- Hot source (e.g. syringe ~400MBq \* 1 minute acquisition, TBC), placed
  roughly on axis of scanner

- any radionuclide without third gamma

- 2 acquisitions: one with source just outside (axial) FOV (at the
  “head” side), one 50cm (TBC) away (at the “head” side)

#### How to test:

- Ideally non-reconstruction test: histogram prompt data, compute
  randoms estimate, should be equal within statistical limits.

### CYLINDER_DECAY: Uniform cylinder (fast decaying radionuclide)

#### Aims:

- Testing dead-time information (including pile-up) and randoms

#### Acquisition(s):

- Cylinder roughly in center of scanner with high-activity decaying over
  several half-lives. A cylindrical fillable phantom provided by the
  scanner vendor can be used. If such a phantom is not available, then
  it is recommended any phantom with a uniformly filled cylindrical
  compartment of a diameter of at least one third of the scanner's
  transaxial FOV diameter and height at least one fourth of the scanner's
  axial FOV length but never exceeding a height of 25cm. The starting
  activity should be such that the SUV is at least 10, which always
  accounts for the variable volume of the cylindrical phantom.

  - Can be C-11, N-13, Rb-82, or Ga-68

  - Can be 1 acquisition or several, no movement

#### How to test:

- non-reconstruction test:

  - histogram coincidence data, subtract randoms estimate, correct for
    dead-time. Test if data remains constant over time.

  - singles histogram data, correct for dead-time. Test if data remains
    constant over time.

### CYLINDER_HIGHCOUNTS: Uniform cylinder (slow decaying radionuclide)

#### Aims:

- Testing normalisation, calibration

#### Acquisition(s):

- Cylinder roughly in center of scanner

- Low to medium activity, long duration

- Report activity, as measured in the dose calibrator for which the PET
  scanner was calibrated for, and volume.

#### How to test:

- Ideally non-reconstruction test: histogram data, normalise, compare
  with analytic model.

#### Notes:

- Can be F18 water-filled, or Ge-68

- Ideally one acquisition, no movement

- Test depends on good estimates for randoms, dead-time and scatter.
  Alternative ideas?

### GANTRY_ALIGNMENT: Gantry alignment test

#### Aims:

- Test alignment information between CT (or MR) and PET gantry

#### Acquisition(s):

- vendor-specific alignment phantom, following vendor protocol

#### How to test:

- Backproject or reconstruct (depending on phantom) PET data with
  information on gantry coordinate system as provided in PETSIRD. Check
  alignment with CT DICOM image.

### ACR: ACR phantom (standard QC test)

#### Aims:

- Overall image quality tests

#### Acquisition(s):

- ACR PET phantom test with F-18 set for a 10mCi equivalent study. See
  <https://accreditationsupport.acr.org/support/solutions/articles/11000062800-phantom-testing-pet-revised-4-30-2026->

- Optionally Ga-68 scan (to assess prompt gamam effects in quantitative
  data corrections

## Targeted scanners

Notes: modern scanners, but adding scanners currently supported by STIR
(and hence STIR2PETSIRD)

- Siemens: mMR, Vision 600, Quadra

- GE: Omni Legend (RDF10), DMI (RDF10), Signa PET/MR (RDF9)

- UIH: TBC

- Positrigo: NeuroLF

## Data organisation and uploading

### Folder structure

Please organise in folders as follows, with data in .tar.gz or .zip
files folders per acquisition (e.g. for different point source
locations). Do not use spaces in filenames. Do not combine all
acquisitions in one huge file.

Note: for multi-bed position scans, there might be 1 or more list-mode
files for one “acquisition”. This is vendor software version dependent.

- README.md\
  Textual description of data with as much detail as possible. Please
  use the skeleton below.

- photos/\
  any photos of set-up

- acq1.tar.gz (or acq1.zip)\
  archive with following files/folders

  - README.md\
    Brief description of this particular acquisition

  - list-mode/\
    List-mode data as exported from the scanner console

  - AC/\
    attenuation CT or attenuation MR dicom series

  - vendor_recon/\
    Images reconstructed with vendor software (DICOM) (all corrections
    applied, exact reconstruction settings that you use are not
    important, but should be reported)

- acq2.tar.gz\
  (where appropriate, subfolder structure as above)

- calibration.tar.gz (or calibration.zip)\
  Normalisation/calibration files (in the format supplied by the vendor)

### Uploading

Please upload this data to a permanent store. In the following, we
assume upload to Zenodo, but similar repositories are also acceptable.

> [!WARNING]
> Zenodo has a 50GB limit per record (although personal quota can be
> used to increase this somewhat). This might be problematic for some
> acquisitions.

We would like the data on a permanent store so that we can use a Digital
Object Identifier (DOI) to cite your data. Note that DOIs are intended
to be permanent identifiers, so files uploaded to Zenodo cannot be
changed once published. Zenodo provide a sandpit site where users can
est file uploads and downloads without creating a permanent DOI. If you
are not familiar with Zenodo uploading, it is useful to try the sandpit
site before creating a permanent public record.

> [!TIP]
> If you wish to login to the [Zenodo sandbox site](https://sandbox.zenodo.org)
> using Orcid credentials, we recommend
> first logging in to the [main Zenodo site](https://zenodo.org) and
> then going to the Zenodo sandpit site, otherwise the system can get
> confused and try to validate you using an Orcid sandpit.

For more information, see the [Zenodo upload
instructions](https://help.zenodo.org/docs/deposit/create-new-upload/).

We manage a Zenodo community called
[Synerbi](https://zenodo.org/communities/synerbi/records?q=&l=list&p=1&s=10&sort=newest)
that provides links to data associated with this community. We encourage data
to be part of this community. Users with data on Zenodo can request that
existing data be linked to this community. Alternatively, you
can [upload directly to this
community](https://zenodo.org/uploads/new?community=synerbi). We will
likely also create an ETSI Zenodo community.

### README.md skeleton for description of phantom data

Please
use [markdown](https://www.markdownguide.org/basic-syntax/) format
(which is just plain text. Do not save as a Word file or similar).\
Note: newlines after “headings” are important.

```markdown
#  Title (Overall description)

PET phantom dataset acquired on (name of scanner) according to the
ETSI test-data protocol v1.0.0, ALLCAPSNAME dataset

## Phantom description

Some text here, could refer to your pictures: ![Some title](./photos/PhantomPicture.png)

## Acquisition information

### Institution

Some text here on where acquired, optionally including names of

personnel.

### Scanner model

(please be as specific as you can, model, software version, …)

### Acquisition Date

Use format 19 MAY 2024

### Radiopharmaceutical and nuclide information

e.g. FDG, F-18

### Preparation protocol

Some text on set-up, ideally including expected activities in various

inserts (cross-calibrated to start of PET scan)

### Acquisition protocol

Some text on how the scan was performed, e.g. CT was acquired with

helical CT, keV, mA, rotation-speed, pitch, ... A 40 min PET scan was

performed in list-mode.

#### acq1

Some brief text on this acquisition

#### acq2

### Reconstruction settings

Some text on what the reconstructions settings were
(e.g. OSEM, 3 iterations, 7 subsets, post-filter (2D Gaussian with FWHM
xxx, and "standard" z-filter)
```
