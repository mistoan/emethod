# E-methodHW – Polynomial and Rational Function Approximations for FPGAs

***
***

## Project Overview

This project proposes an automatic method for the evaluation of functions via polynomial or rational approximations and its hardware implementation, on FPGAs.

These approximations are evaluated using Ercegovac's iterative E-method <sup>[1](#ref1)</sup>  <sup>[2](#ref2)</sup>  adapted for FPGA implementation.

The polynomial and rational function coefficients are optimized such that they satisfy the constraints of the E-method.

If you would like to find more technical details, as well as comparisons with other tools that offer hardware function approximations, have a look in the [research report]. You can also have a look at the [slides] of the presentation that Silviu-Ioan Filip gave at ARITH25, presenting the project.

---

<a name="ref1">1</a>: M. D. Ercegovac, “A general method for evaluation of functions and computation in a digital computer,” Ph.D. dissertation, Dept. of Computer Science, University of Illinois, Urbana-Champaign, IL, 1975.

<a name="ref2">2</a>: M. D. Ercegovac, “A general hardware-oriented method for evaluation of functions and computations in a digital computer,” IEEE Trans. Comput., vol. C-26, no. 7, pp. 667–680, 1977.

[research report]: ./doc/e-metod_research_report.pdf
[slides]: ./doc/slides_arith2018_Silviu_Filip.pdf

***
***

## Installation Instructions

**E-methodHW** depends on the *efrac* library and on *FloPoCo*.
In turn, *efrac* depends on: [gmp], [mpfr], [mpreal], [fplll], [eigen] and [qsopt_ex].
You can follow instructions on the respective websites in order to install them on your system.
*[FloPoCo]* has a pretty extensive list of dependencies, that you can check on the project's page. You can also use their [one-line install] to get everything ready (you can leave out the download and install of the actual library, as everything required is provided in this repository).
To make things easier (and hoping you have one of the supported distributions), here is the required shell command:
```sh
sudo apt-get install g++ libgmp3-dev libmpfr-dev libfplll-dev libxml2-dev bison libmpfi-dev flex cmake libboost-all-dev libgsl0-dev && wget https://gforge.inria.fr/frs/download.php/33151/sollya-4.1.tar.gz && tar xzf sollya-4.1.tar.gz && cd sollya-4.1/ && ./configure && make -j4 && sudo make install
```

[gmp]: https://gmplib.org/
[mpfr]: https://www.mpfr.org/
[mpreal]: www.holoborodko.com/pavel/mpfr/
[fplll]: https://github.com/fplll/fplll
[eigen]: https://eigen.tuxfamily.org/
[qsopt_ex]: https://github.com/jonls/qsopt-ex

[FloPoCo]: http://flopoco.gforge.inria.fr/
[one-line install]: http://flopoco.gforge.inria.fr/flopoco_installation.html

In order to use **E-MethodHW** you need to start by either `cloning` (follow the instructions at the top of this page) or `downloading` and then unpacking the archive.
Asuming that the extracted project is in the `emethod` directory,  building the project can be done using the following commands:

```sh
cd emethod
cd main
cmake .
make all
```
There are several build options, that can be passed directly to cmake.
```cmake
-DCFG_TYPE=Release|Debug
```
set by default to `Release`, will generate debug information and show debug messages during compilation if set to `Debug`.
```cmake
-DCFG_FLOPOCO=Lib|LibExec
```
set by default to `Lib`, allows building FloPoCo as either library only, or library and executable (useful for comparing the performance of E-methodHW, as described in the research report) when set to `LibExec`.

If you would like to remove the files generated during build or subsequent runs of `emethodHW`, you can use the `clean-all` make target by typing:
```sh
make clean-all
```
If you would like to remove the files generated during the build of `emethodHW`, use the `clean-cmake-files`:
```sh
make clean-cmake-files
```
If you would like to remove only the files generated during the execution of `emethodHW`, use the `clean-run-files`:
```sh
make clean-run-files
```

***
***

## Usage

### Passing Parameters

There are several ways of passing paremeters to `emethodHW`. In decreasing order of their priority, they are:
   1. *command line*
   2. *default values*
   3. *configuration file*

Parameters on the command line are passed as:
```sh
--<parameter_name>=<value>
```
The exact format of `<value>`depends on its actual format (for example, a string is specified between single or double quotes).

Parameters in the configuration file are passed as:
```sh
<parameter_name>=value
```
each on an individual line. You can insert lines that start with *#* in orderto separate the parameters into groups.

For details on the default values,  pull up the help, by typing:
```sh
emethod --help-all
```
or have a look at the [CommandLineParser.cpp] file.

If no parameters are provided, `emethodHW` is configured to run **Example 1** from the [research report].
Not providing some of the required parameters results in the usage of the default one, which might not meet your expectations.

[CommandLineParser.cpp]: ./main/CommandLineParser.cpp

### Program Parameters

The following is a list of parameters and their meaning:
	
**Mandatory functional paremeters**
- **function, f** - the function to evaluate; e.g. "((x * 4.5)^4 + 1)^0.5"
- **weight, w** - the weight of the function to evaluate
- **delta, d** - the value of *delta* in the E-method algorithm
- **radix, r** - the radix used for the implementation
- **lsbInOut, l** - the weight of the *least significant bit* of the input and the output in the used fixed-point format

**Optional functional parameters**
- **xi, x** - the value of *xi* in the E-method algorithm
- **alpha, a** - the value of the **alpha** parameter in the E-method algorithm
- **scaleInput** - enables/disables the generation of the hardware for scaling the input to the circuit
- **inputScalingFactor** - value of the input scaling factor
- **msbInOut, m** - the weight of the *most significant bit* of the input and the output in the used fixed-point format

**Optional perfomance parameters**
- **verbosity** - the verbosity level, controling the amount of messages printed at the command line; ranges from 0=no messages, to 5=all messages
- **pipeline** - enables/disables the generation of a pipeline for the resulting hardware implementation
- **frequency** - the target frequency of the resulting hardware implementation (take this with a grain of salt, the generator tries to get a close-enough to the set value)
- **testbench** - the number of testcases to be generated; if set to *0*, no tests are generated

**Miscellaneous options**
- **version, v** - version of emethodHW and of the used libraries
- **help, h** - produce the help message
- **help-all** - produce an extended help message
- **configFile, c** - the name of the file containing the configurations, if it exists

***
***