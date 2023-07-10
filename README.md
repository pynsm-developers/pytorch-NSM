# Non-negative similarity matching in PyTorch

[![Python 3.9](https://img.shields.io/badge/python-3.9-green.svg)](https://www.python.org/downloads/release/python-360/)
[![license: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](https://opensource.org/licenses/MIT)

This is an implementation of non-negative similarity matching (NSM) for PyTorch focusing on ease of use, extensibility, and speed.

## Table of Contents

- [Installation](#installation)
- [Example usage](#example-usage)
- [Features](#features)
- [Questions?](#questions)

## Installation

It is strongly recommended to use a virtual environment when working with this code. The installation instructions below include the commands for creating the virtual environment, using either `conda` (recommended) or `venv`.

### Using `conda`

If you do not have `conda` installed, the easiest way to get started is with [Miniconda](https://docs.conda.io/en/latest/miniconda.html). Follow the installation instructions for your system.

Next, create a new environment and install the necessary pre-requisites using

```sh
conda env create -f environment.yml
```

for using the CPU or

```sh
conda env create -f environment-cuda.yml
```

for using an NVIDIA GPU. Note that most Macs do not have an NVIDIA GPU. If your Mac uses the newer Apple chips, you may be able to use ``device = mps`` to get GPU acceleration.

Finally, activate the environment and install the `pynsm` package:

```sh
conda activate pynsm
pip install -e .
```

The `-e` marks this as an "editable" install — this means any updates to the code will automatically take effect without having to reinstall the package. This is mainly useful for developers.

Note that you will also need to install `jupyter` to run the notebook demos and `pytest` if you want to run the tests.

### Using `venv`

Before creating a new virtual environment, it is best to ensure you're not using the system version of Python — this is often badly out of date. Some options for doing this are outlined in [The Hitchhiker's Guide to Python](https://docs.python-guide.org/starting/installation/#installation-guides), although many options exist. One advantage of using `conda` is that this is done for you.

Once you have a proper Python install, create a new virtual environment by running the following command in a terminal inside the main folder of the repository:

```sh
python -m venv env
```

This creates a subfolder called `env` containing the files for the virtual environment. Next we need to activate the environment and install the necessary pre-requisites

```sh
source env/bin/activate
pip install -r requirements.txt
```

To use an NVIDIA GPU, run

```sh
source env/bin/activate
pip install -r requirements-cuda.txt
```

Note that most Macs do not have an NVIDIA GPU. If your Mac uses the newer Apple chips, you may be able to use ``device = mps`` to get GPU acceleration.

Finally, install the `pynsm` package:

```sh
pip install -e .
```

The `-e` marks this as an "editable" install — this means that changes made to the code will automatically take effect without having to reinstall the package. This is mainly useful for developers.

Note that you will also need to install `jupyter` to run the notebook demos and `pytest` if you want to run the tests.

## Example Usage

See the notebooks in the [`examples`](examples) folder to get started with the package.

## Features

### Similarity matching with arbitrary encoder

The code defines a neural network module called `SimilarityMatching`. This model takes an arbitrary encoder module and generates a "competitor" layer -- a linear layer that enforces competition between the different output channels from the encoder. Natural examples of encoder layers are fully-connected layers and convolutional layers. The `forward` method performs the forward pass of the model, which involves running to convergence the dynamics involving the lateral competitor connections. The model also includes a method for calculating the loss from which the plasicity rules can be derived.

### ZCA Whitening

The code includes a ZCA whitening function `computeZCAMatrix` that computes the ZCA matrix for a set of input observations `X`. The function performs normalization, reshaping, covariance computation, singular value decomposition (SVD), and builds the ZCA matrix. The whitening transformation is implemented in the `ZCATransformation` class, which takes the ZCA matrix and transformation mean as inputs and applies the transformation to a given tensor image.

### Training the Model

One of the key design goals was to make using the `SimilarityMatching` modules be as seamless as possible. Indeed, training the module can be done in precisely the same way as training any other PyTorch module. The only difference is that there is a canonical choice for the loss function, and that can be obtained from the `loss()` method of the module. See the example notebooks for details on the training.

### Classification

The pre-trained network is used in a supervised fashion to perform classification. Specifically, the embeddings generated by the `SimilarityMatching` module are followed by a max-pooling layer and then passed through an SVM (using `SGDClassifier` from `sklearn`). We obtain good accuracy on the test set.

### Supervised NSM

The code also introduces a module called `SupervisedSimilarityMatching` class, which implements supervised learning. It includes additional functionality for handling labeled data during training. It is a special case of `MultiSimilarityMatching`, which attempts to maximize the similarity between the circuit's output and multiple inputs.

## Questions?

Please contact us by opening an issue on GitHub.
