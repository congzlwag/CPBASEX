# CPBASEX

[pBASEX](http://dx.doi.org/10.1063/1.1807578) Abel Inversion without Polar Rebinning... in MATLAB and Python. A broader introductionto Abel inversion and the pBASEX algorithm is available in [README.pdf](https://github.com/e-champenois/CPBASEX/blob/master/README.pdf).

Quick feature list:
- Implementation of the pBASEX Abel inversion algorithm by direct sampling of the basis functions on a Cartesian grid rather than rebinning the data into polar coordinates and introducing some error.
- Use of singular value decomposition to speed up the least-squares fitting step.
- L2-regularized (also fast) or pixel weighted (slower) fitting available.
- Parallelized and efficient Abel transformed basis function calculations.

# Installation

## Python

### From Source (RECOMMENDED)
```sh
$ git clone https://github.com/congzlwag/CPBASEX.git
```
This will clone this fork repo into folder `CPBASEX`. No building is necessary. To import the package, adding `CPBASEX/pbasex-Python/pbasex/` to `sys.path` is sufficient.

IF you perfer a complete installation
```sh
$ cd CPBASEX
$ python setup.py install
```

### Using Pip 
[pip](https://pip.pypa.io/en/stable/) is a great package manager. From the command line (perhaps using a [virtual environment](https://docs.python.org/3/tutorial/venv.html)), simply install the [PyPI CPBASEX distribution](https://pypi.python.org/pypi/pbasex):
```sh
$ pip3 install pbasex
```

This will install the Python version as well as several dependencies:
- [NumPy](http://www.numpy.org/), a popular Python package for numerical programming.
- [H5Py](http://www.h5py.org/), an HDF5 file storage implementation.
- [Cython](http://cython.org/), C-extensions for Python, required by the H5Py package.
- [Quadrant](https://pypi.python.org/pypi/quadrant), a small package to deal with folding and unfolding images to and from the image quadrants.

Pre-installing Cython and H5Py will make the installation process much faster, as opposed to installation using pip dependencies:
```sh
$ pip3 install cython
$ pip3 install h5py
$ pip3 install pbasex
```

The [dill](https://pypi.python.org/pypi/dill) is also required to parallelize the Abel transformed basis function sampling step:
```sh
$ pip3 install dill
```
**Note** As of May2024, the PyPI CPBASEX distribution is still oudated compared to the git repo, specifically in loading h5 datasets. The git repo has been updated in commit [00ef967](https://github.com/e-champenois/CPBASEX/commit/00ef96760aafac042e81a3c5f4525c884e08d014). Until the PyPI distribution is also updated, following the pip installation, it is necessary to manually fix the source code according to commit [00ef967](https://github.com/e-champenois/CPBASEX/commit/00ef96760aafac042e81a3c5f4525c884e08d014). 


## MATLAB
Simply add `CPBASEX/pbasex-MATLAB/` to the MATLAB path.

# Running the code

The pBASEX algorithm works in two steps. First, the Abel transformed basis functions must be sampled. Next, a least-squares fit inverts measured data. Sample code for the first ([examples/save_gData.m](https://github.com/e-champenois/CPBASEX/blob/master/examples/save_gData.m), [examples/save_gData.py](https://github.com/e-champenois/CPBASEX/blob/master/examples/save_gData.py)) and the second ([examples/sample_pbasex.m](https://github.com/e-champenois/CPBASEX/blob/master/examples/sample_pbasex.m), [examples/sample_pbasex.py](https://github.com/e-champenois/CPBASEX/blob/master/examples/sample_pbasex.py)) is available. The first step must only be run once, with the results being saved locally. The second step can be run with these results for any amount of new raw image data.

In save_gData, several key parameters that can be changed are:
- the number of radial pixels (quadrant.resizeFolded can also be used to appropriately resize a folded image)
- the ratio of radial pixels to radial basis functions
- the number of angular basis functions
- the functional form of the radial basis functions.

In sample_pbasex, the steps required to fold an image and invert it are shown. Various forms of the result are also plotted. This requires the [Matplotlib](https://matplotlib.org/) library, which can also easily be installed with pip:
```sh
$ pip3 install matplotlib
```

# Code wish list

  - Propagating experimental errors (as mentioned by GitHub user phockett) through the inversion procedure. Although implementation optimizations hide this fact, all calculations here can be represented as matrix multiplications, such that error propagation can be implemented as Var(y) = A * Var(x) * A', where y = A * x. I have this partially implemented locally, although there it is a bit difficult to test for accuracy. Also, if Var(x) is a full covariance matrix, calculations get prohibitively large, so I have only tried using diagonal covariance matrices (no coupled errors).
  - Allowing for odd angular momentum contributions, accounting for non-isotropic target ensembles. This would need a bit of rework since the single quadrant picture is no longer accurate.
  - Rework the Python code into an object-oriented architecture (similar to [cart2polar](https://pypi.python.org/pypi/cart2polar)).
