= Z3str3RE
:toc: left
:stem:
This file describes how to compile and install Z3str3RE and setup the benchmark infrastructure including the used benchmark sets to reproduce the results presented in the corresponding submission.We present a novel length-aware solving algorithm for the quantifier-free first-order theory over regex membership predicate and linear arithmetic over string length. We implement and evaluate this algorithm and related heuristics in the Z3 theorem prover. A crucial insight that underpins our algorithm is that real-world instances contain a wealth of information about upper and lower bounds on lengths of strings under constraints, and such information can be used very effectively to simplify operations on automata representing regular expressions. Additionally, we present a number of novel general heuristics, such as the prefix/suffix method, that can be used in conjunction with a variety of regex solving algorithms, making them more efficient. We showcase the power of our algorithm and heuristics via an extensive empirical evaluation over a large and diverse benchmark of 57256 regex-heavy instances, almost 75% of which are derived from industrial applications or contributed by other solver developers. Our solver outperforms five other state-of-the-art string solvers, namely, CVC4, OSTRICH, Z3seq, Z3str3, and Z3-Trau, over this benchmark, in particular achieving a 2.4x speedup over CVC4, 4.4x speedup over Z3seq, 6.4x speedup over Z3-Trau, 9.1x speedup over Z3str3, and 13x speedup over OSTRICH.  The implemented solver will be merged into the mainline Z3 solver (https://github.com/Z3Prover/z3) once the algorithm is published.

== Licences
Our license file is called ``LICENSE.txt``. Since we included all solvers to whom we compared
our algorithm we included their licenses as well. They are located in the subfolder
``OTHER_SOLVER_LICENSES``.

== The Solver
=== Quick Start
We provide a VirtualBox virtual machine running Ubuntu 20.04 LTS being based on the Artifact Evaluation VM used for TACAS 21 (https://zenodo.org/record/4041464#.YBGm5C1Q1QI) by Sebastian Hjort Hyberts, Peter Gjøl Jensen and Thomas Neele and a package tweaked towards installing it on your own machine running Ubuntu 20.04 LTS.

1. The virtual machine is available at https://zenodo.org/record/4477692#.YBlbaC1Q3SU After downloading load the virtual image within your VirtualBox environment. Whenever needed use username "re" and password "re". You can skip the "Install the prerequisites" section, since the virtual machine is already preconfigured.
2. The installation package is part of this package. Extract the content by using 
	```tar xf cav21.tar.gz```
	Follow the instructions in the next section

=== Install the prerequisites 
To install the required packages we provide a script called ``installTools.sh``. Executing
	```bash installTools.sh```
installs all dependencies needed for running our solver and reproducing our empirical evaluation.

In order to reproduce the figures presented in the paper you need a LaTeX environment. The following command downloads the 
required packages for Ubuntu like systems


```
sudo -S apt-get -y install latexmk texlive-latex-recommended texlive-latex-extra texlive-fonts-extra asciidoctor
```

Note that due to the size of the package, we omitted them within our reproduction archive. 
Also, these packages are not needed to reproduce our results. 

=== Z3str3RE's source code
Within this subsection we briefly show how to use Z3str3RE independently and point to the most relevant parts within the source code wrt. to our paper.

==== Compiling Z3str3RE
We include a bash script named ``compileRegExSolver.sh`` which compiles the provided source code. To compile the solver you simply have to execute 

```
bash compileRegExSolver.sh
```

The executable is afterwards located within a subfolder `build` within the source code, namely `RegExSolver/build/z3`.

==== Using Z3str3RE
Since Z3str3RE is based on Z3str3 it accepts SMT-LIB 2.5 as input format. We are now assuming that you successfully compiled z3str3RE using the above scripts and changed your directory to `RegExSolver/build`. An alternative is navigating to our pre-build binary located in `SolverBinaries/RegExSolver` 
To use our Z3 including the presented regex algorithm and all heuristics you have to execute the following command:

```
./z3 smt.string_solver=z3str3 smt.str.tactic=arr smt.arith.solver=2 dump_models=true <smtlibfile>
```

To play around you can run the used benchmarks. They are located within the ZaligVinder folder `zaligvinder/models/`. An example running on of our instances is the following:

```
./z3 smt.string_solver=z3str3 smt.str.tactic=arr smt.arith.solver=2 dump_models=true ../../zaligvinder/models/automatark/simplenew/instance1000.smt2 
```

Additionally we added three SMT-LIB formulas proposed by a reviewer of our paper (Thanks!). They are located within the `additional_inputs` folder within the root of our artifact.

We provide additional parameters to deactivate the presented heuristics, that are 

- `smt.str.regex_automata_construct_linear_length_constraints=false` to disable deriving length information out of a constructed automaton,
- `smt.str.regex_automata_construct_bounds=false` to disable the arithmetic solver integration,
- `smt.str.regex_prefix_heuristic=false` to disable the prefix heuristics,
- `smt.str.regex_automata_length_attempt_threshold=0` and `smt.str.regex_automata_failed_intersection_threshold=0` to disable the lazy intersection of regular languages.

Combine all of these parameters to completely disable the heuristics, i.e.

```
./z3 smt.string_solver=z3str3 smt.str.tactic=arr smt.arith.solver=2 dump_models=true smt.str.regex_automata_construct_linear_length_constraints=false smt.str.regex_automata_construct_bounds=false smt.str.regex_prefix_heuristic=false smt.str.regex_automata_length_attempt_threshold=0 smt.str.regex_automata_failed_intersection_threshold=0 <smtlibfile>
```

==== Relevant parts within the source code
The source code is located within the folder `RegExSolver`. We implemented the presented algorithm as an extension to the theory of strings of z3str3. Our algorithm including the heuristics is located in a single file at
`src/smt/theory_str_regex.cpp`. Minor comments ease mapping the implementation to the parts within our paper. 
Our additional algorithm is triggered within z3str3's core implementation located in `src/smt/theory_str.cpp`.

=== Reproducing the results
We provide two scripts to reproduce the presented results using the benchmark framework ZaligVinder.

==== Repeating the run using all solvers on all presented benchmarks:
```
bash runBenchmarks.sh
```
==== Repeating the run including the heuristics analysis:
```
bash runBenchmarksHeuristics.sh
```

Within our empirical evaluation we use 57256 different instances. Running all these instances on a standard computer would take an extremely long time. Therefore, we added two additional runner scripts executing our experiments on a previously randomly selected set of benchmarks. To execute this "smaller" set, proceed as follows:

====  Repeating the run using all solvers on a subset of the presented benchmarks (approximately 5 minutes):

```
bash runBenchmarksSmall.sh
```
==== Repeating the run including the heuristics analysis on a subset of the presented benchmarks (approximately 6 minutes):

```
bash runBenchmarksHeuristicsSmall.sh
```

After the ZaligVinder run has finished a webserver is started. You can review the results by guiding your browser to http://localhost:8081.

=== Further post analysis steps
Within the ZaligVinder folder ``zaligvinder`` we stored the database of our experimental evaluation.

==== Inspecting the results within the web browser:
Execute the following commands to review our results within ZaligVinder's web gui:

```
cd zaligvinder
python3 startwebserver.py cav.db
```

Afterwards navigate to http://localhost:8081 in your web browser.

==== Generate an tables, plots and ASCII Doctor web page to review the results:
Execute the following commands

```
cd zaligvinder
python3 tablegen.py
```
which will open a terminal gui. Select ``cav.db`` as database and a place to store the output file -- entering no output name stores the output data within the folder ``zaligvinder/external_data/<today's_date>``. Afterwards you are able to select the benchmark sets of your choice being present within our run and the solvers. Note, Z3str3-base is the implementation of our regex solver. Z3str3RE-none, Z3str3RE-li, Z3str3RE-psh, Z3str3RE-ali, and Z3str3RE-asi represent solvers being part of our heuristics experiment.

Next step is selecting whether you want to generate the cactus plots, tables or the ASCII Doctor page.

===== Tables and Plots.
Tables and plots need to be compiled by using ``latexmk``. Simply execute

```
latexmk -pdf <yourFileName>
```

to generate a PDF.

===== Generating HTML for the ASCII doctor page.
To view the ASCII Doctor page conveniently in your browser execute

```
asciidoctor <yourFileName>
```

which will create a ``<yourFileName>.html`` file. To open firefox execute ``firefox <yourFileName>.html``.

You can perform these post analysis steps on your own generated database too. To do so instead of using ``cav21.db`` within the terminal gui simply select your own generated database having a name similar to ``CAV21_results_1611768687.585239.db``.
