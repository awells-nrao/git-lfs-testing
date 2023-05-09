## Repository for CASA tests datasets
This git lfs repository stores datasets used in the CASA tests. At run time, the CASA tests will look for
the data they need. In order to run the CASA tests on your local machine you will need to have a copy
of the datasets that are stored in this repository. There are 2 options to checkout the data, depending
on which tests you will run. For the CASA development team, the best solution is to checkout the
entire repository as shown below.

### Checkout the entire casatestdata repository
The entire casatestdata repository is about ~ 100 GB. To save some space, you can avoid downloading the
stakeholder directory as explained in the next section.

```
-bash-4.2$ git clone -q --recursive https://open-bitbucket.nrao.edu/scm/casa/casatestdata.git
```

### Sparse checkout of only the stakeholder data directory
The stakeholder directory contains relatively larger datasets that are only used by the CASA stakeholders tests.
To only download these datasets, follow these directions for a sparse checkout.

```
-bash-4.2$ git clone --no-checkout https://open-bitbucket.nrao.edu/scm/casa/casatestdata.git
-bash-4.2$ cd casatestdata
-bash-4.2$ git show HEAD:stakeholder-data | bash
```

### On demand sparse checkout of other directories
If you are only interested in downloading parts of the data for other purposes other than running the CASA tests,
you can setup a sparse checkout yourself by following these instructions. I suggest you rename the top directory
to something else other than casatestdata to avoid confusion when working with both repos.
In the following example we will download only the image data from the repository.

```
-bash-4.2$ git clone --no-checkout https://open-bitbucket.nrao.edu/scm/casa/casatestdata.git image-data
-bash-4.2$ cd image-data
-bash-4.2$ git config core.sparseCheckout true
-bash-4.2$ echo image/* >> .git/info/sparse-checkout
-bash-4.2$ git checkout master
```

### Upload data to the repository
Contact the CASA verification test team to upload new datasets to the repository.

### Description of the content of the repository
```
├── digitalimage
├── fits
├── image
├── measurementset
├── other
├── performance
├── regression
├── sdm
├── stakeholder
├── table
├── text
├── unittest
└── uvfits
```

#### digitalimage
Contains any non-CASA image files such as: JPG, TIFF, PNG, etc.

#### fits
Any FITS (Flexible Image Transport System) files.

#### image
Contains datasets in CASA image format of any type (PB, mask, image, etc.).

#### measurementset
For MeasurementSet files and alike (.e.g. Multi-MS). The measurementset directory is sub-divided by main
telescope type.
   * alma
   * almasd (ALMA Single-dish MeasurementSets)
   * evla
   * vla
   * other (any other MeasurementSet)

#### other
Contains any data format that is not described in one of the other data directories. Examples of other types of
data format are: ASAP, VLA input data, region files.

#### performance
Data used by CASA performance tests. The sub-directories are separated for each performance test script,
which stores symbolic links to the real data. e.g. directory tclean\_mem\_setweighting contains symlinks for 
datasets used by test\_perf\_tclean\_mem\_setweighting.py.

The performance test scripts represented by each sub-directory are stored in [casa6/casatests/performance](https://open-bitbucket.nrao.edu/projects/CASA/repos/casa6/browse/casatests/performance). 


#### regression
Data used by CASA regression tests. The sub-directories are separated for each regression test script,
which stores symbolic links to the real data. e.g. directory fits\_import\_export stores symbolic links
for datasets used in test\_regression\_fits\_import\_export.py.

The regression test scripts represented by each sub-directory are stored in [casa6/casatests/regression](https://open-bitbucket.nrao.edu/projects/CASA/repos/casa6/browse/casatests/regression). 


#### sdm
Science Data Model datasets used by some importing tasks in CASA (e.g. ASDM).

#### stakeholder 
Datasets used for specific stakeholders tests. Usually these datasets are large and represent use-cases
of specific stakeholders. The datasets in the stakeholder directory exceptionally, are not symbolic links
but the real datasets.
The stakeholder directory is sub-divided into specific stakeholders such as: ALMA, VLASS, etc. The stakeholders
test scripts are found in [here](https://open-bitbucket.nrao.edu/projects/CASA/repos/casa6/browse/casatests/stakeholder).

Users who wish to only run stakeholders tests can do a sparse checkout of the stakeholder
directory as specified above.

#### table
Contains tables generated by CASA; e.g. tables generated by the Table tool and calibration tables
of any type (tsys, bandpass).

#### unittest
This directory is sub-divided by test name. Each sub-directory contains symbolic links to the actual datasets
used in the tests. For example, directory sdcal contains symlinks to datasets used by test\_sdcal only.

The following are rules to apply when adding content to these directories:

   * Only store symbolic links to the datasets needed by the test scripts.

   * The symbolic link should have the same name of the original file for easy searching later.

   * The only real files allowed to store in these directories are those used for comparison inside the tests. 
   In that case, create a directory called **taskname_reference** or something meaningful and store the comparison 
   files in the test sub-directory. See examples in `./unitttest/imdev/imdev_reference` or `./unittest/gencal/alma_reference`.

   * If the datasets are auto-generated by some script, you can also store the scripts in the test directory
and name it "scripts".

   * Do not modify the files pointed by the sym links. If you need a modified version of an existing
dataset, create a new one in the appropriate place (e.g. measurementset, table, etc.) and name it accordingly, 
to describe the modifications done on the dataset.

```
e.g. ngc5921_spw0_scan1_vla.ms
e.g. uid_A002_X85_chanaverage_alma.ms
```

   * Create a symlink to the new file in the test directory.


#### text
Any text file (ASCII, rtf, csv).

#### uvfits
Contains uvfits files, such as those used importuvfits.

### Naming convention for files stored in the repository
As much as possible, try to give a descriptive name to the dataset so that
it is clear the content or main characteristics of each dataset. Some examples and suggestions
are shown below:

   * ngc5921\_with\_flags\_vla.ms
   * sim\_ngc5921\_single\_source.ms
   * sim\_mosaic\_single\_channel\_alma.image

sim should be used to denote a simulated dataset.

### How to use the datasets in the CASA test scripts
The datasets should be stored in the most appropriate directory based on
their data formats. If no good match exist for a directory, store the dataset in the "other" directory. MeasurementSets
are separated per telescope in different sub-directories and data coming from any other
telescope should be added to measurementset/other.

For each dataset used in a test script, add a symbolic link (relative, not absolute path)
to the appropriate directory for the test inside "unittest/taskname".

Do not use symlinks from other test sub-directories.

Add any reference/comparison data to the test directories themselves in a separated
sub-directory.

Add any script to generate data in the test directories themselves, in a separated sub-directory.

