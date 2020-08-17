---
layout: default
title: Getting Started
---

Z3str3 is a constraint solver for the quantifier-free theory of string equations, the regular-expression membership predicates, and linear arithmetic over the length functions. Z3str3 is now part of the Z3 theorem prover's main codebase, and is the primary string solver in Z3.

**Latest stable release: [Official Z3 Repository](https://github.com/z3prover/z3)**

## Install

1. Check out the latest version of Z3 from the [Z3 Git repository](https://github.com/z3prover/z3).
2. Follow the included instructions to build Z3 from source, or download a binary release for your platform. Building with CMake is recommended.
3. Z3str3 can be used directly from the Z3 binary or the Z3 API.
4. Enabling Z3str3 explicitly is recommended. This is done by setting the `smt.string_solver` parameter to `z3str3`. On the command line, this is passed as an argument before the name of the instance to solve, e.g. `z3 smt.string_solver=z3str3 instance.smt2`. Parameters can also be set using the Z3 API. Setting this option inside of an instance is _not recommended_ as this option is not portable to other tools.

## Acknowledgements

We would like to thank the following people and teams for their valuable contributions, comments or suggestions:

* Special thanks to the S3 string solver team for their generous help on integer/string theory integration.
* CVC4 team for the valuable dicussions
* Benjamin Spencer for suggestions on fixing linking errors
* Julian Thomé for suggestions on building Z3-str on Mac OS X
* Julian Thomé for suggestions and contributions on improving the front-end string constraint encoding interface
* Prof. Nickolai Zeldovich for a bug report and suggestions on Python API