---
title: "Compiling tbronzwaer/raptor"
date: 2022-04-15T16:38:03+05:30
tags: ["raptor", "ubuntu", "18.04", "linux", "GSL", "CBLAS", "ananconda", "make"]
categories: ["build"]
draft: false
---
## Introduction
This post documents steps required to compile [tbronzwaer/raptor](https://github.com/tbronzwaer/raptor.git) and use plotter.py

## Dependencies
### Ubuntu 18.04
Install Ubuntu 18.04 as a VM. (https://releases.ubuntu.com/18.04/)

### Build Dependencies
```bash
sudo apt install gcc build-essential gfortran libpng-dev
```

### GNU Scientific computing Library (GSL)
GSL needs to be compiled from source.
```bash
cd ~
git clone https://github.com/ampl/gsl.git
cd gsl
mkdir build && cd build
cmake .. -G"Unix Makefiles" -DNO_AMPL_BINDINGS=1
make
sudo make install
```

### CBLAS library (Basic Linear Algebra Subprograms)
- Build `blas_LINUX.a`
```bash
cd ~
curl -Ok http://www.netlib.org/blas/blas-3.10.0.tgz
tar zxzf blas-3.10.0.tgz
cd BLAS-3.10.0/
make
```
- Build `libcblas.a`
```bash
cd ~
curl -Ok http://www.netlib.org/blas/blast-forum/cblas.tgz
tar xvzf cblas.tgz
cd CBLAS/
ln -sf Makefile.LINUX Makefile.in
# Change BLIB variable in Makefile.in to "BLLIB = ${HOME}/BLAS-3.10.0/blas_LINUX.a"
make
cd lib/
ln -s cblas_LINUX.a libcblas.a
```
## RAPTOR
### Compile RAPTOR
```bash
cd ~
git clone https://github.com/tbronzwaer/raptor.git
cd raptor
# Add libcblas path in LDFLAGS in makefile
# LDFLAGS = -lm -lgsl -L${HOME}/CBLAS/lib -lcblas
make harm CPU=1
```

### Create Image Data
```bash
cd ~/raptor
bash run.sh
```

## Plotting
### Ananconda
Get latest link for `*.sh` file from https://www.anaconda.com/distribution/
```bash
curl -Ok https://repo.anaconda.com/archive/Anaconda3-2021.11-Linux-x86_64.sh
bash Anaconda3-2021.11-Linux-x86_64.sh
# Follow on-screen instructions to install anaconda and restart Terminal.
```

### Create conda env and activate it
```bash
conda create -n raptor python=3.6.9
conda activate raptor
```

### Install python deps
```bash
conda install numpy
conda install matplotlib
conda install scipy
```

### plotter.py
```bash
cd ~/raptor
python3 plotter.py
ls -l output/
```
