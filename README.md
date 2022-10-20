**Please Note**: the README in the VM at `/home/artifact/stitch-artifact/README.md` is slightly outdated due to various tweaks or additions, including clarifying parts of the instructions and providing the "Additional Artifact Description". So for a smooth evaluation please either `git pull` the changes into the VM or just use any of the other copies of the README: the copy you're reading now; or the one in the base directory of Zenodo; or the one in stitch-artifact.zip on Zenodo; or the one on the [official artifact Github](https://github.com/mlb2251/stitch-artifact/). Sorry for the inconvenience and thank you for helping review!

This is the POPL 2023 Artifact for the tool `stitch` from the paper "Top-Down Synthesis for Library Learning" (Submission #278).

This artifact can be downloaded from either of two places:
- Zenodo: https://zenodo.org/record/7150898
- Github: https://github.com/mlb2251/stitch-artifact

Additionally a VirtualBox VM image is available at the Zenodo link above.

# List of Claims
This artifact evaluates the following claims, taken verbatim from the start of Section 6:
- Claim 1:
  - "Stitch learns libraries of comparable quality to those found by existing deductive library learning algorithms in prior work, while requiring significantly less resources." (Comparison of Stitch to DreamCoder)
  - Paper Section: 6.1
  - Figures: Fig 6, Fig 7, Fig 8.
  - Evaluated in: "Claim 1" section below
- Claim 2:
  - "Stitch scales to corpora of programs that contain more and longer programs than would be tractable with prior work" (benchmarking Stitch on Wong et al. 2022 dataset and presenting results).
  - Paper Section: 6.2
  - Tables: Table 1, Table 2
  - Evaluated in: "Claim 2" section below
- Claim 3:
  - "Stitch degrades gracefully when resource-constrained" (any-time algorithm study).
  - Paper Section: 6.3
  - Figures: Fig 9.
  - Evaluated in: "Claim 3" section below
- Claim 4:
  - "All the elements of Stitch matter" (ablation study).
  - Paper Section: 6.4
  - Tables: Table 3.
  - Evaluated in: "Claim 4" section below
- Claim 5:
  - "Stitch is complementary to deductive rewrite-based approaches to library learning" (The "PyStitch" prototype).
  - Paper Section: 6.5
  - Tables: Table 4.
  - Evaluated in: "Claim 5" section below

# Download, installation, and sanity-testing instructions

We provide both VM Setup and Non-VM Setup instructions below. While the tool `stitch` (i.e. Claims 1-4) has minimal dependencies, the PyStitch prototype presented in Claim 5 has slightly more dependencies and might be easier run in a VM if issues are encountered. All claims can also be evaluated through the VM for simplicity preferred.

## Memory Requirements (RAM)

- Claim 1: <1 GB
- Claim 2: <1 GB
- Claim 3: <1 GB
- Claim 4: 50 GB (we provide full logs of a successful 20 hour run of this ablation study as an additional alternative)
- Claim 5: 20 GB (we provide full logs of successful runs of this prototype system)

## VM Setup

**Please refer to the note at the top of this document about how the README in the VM is outdated - thank you!**

Download the `.ova` VM image from the artifact [Zenodo link](https://zenodo.org/record/7150898).

Download VirtualBox 6.1 (tested on 6.1.38), import the `ova` file with `File > Import Appliance`, **and be sure to adjust the allocated Memory and CPUs as appropriate**:
- see the "Memory Requirements (RAM)" section above; the defaults are *not* enough to run every experiment.
- Also note that 8 cores are necessary if you want to re-run DreamCoder baselines (given as an Appendix) but the rest of the experiments are fine on 1 core.
- 4GB of memory and 1 CPU is enough to run all of the most important experiments, and more is only needed if you opt out of using the logged versions of ablation experiments (Claim 4) and prototype (Claim 5) experiments which are less central to the paper. We have provided full instructions for reproducing everything in addition to providing logs, if resources are available.

VM Info:
- Ubuntu 20.04
- Username: `artifact`
- Password: `artifact`
- Artifact repo is at `/home/artifact/stitch-artifact`
- All dependencies are pre-installed and `stitch` is pre-compiled
- `/home/artifact/stitch-artifact/README.md` is outdated and should either be `git pull`'d or alternatively any other README (like this one you're reading) can be used.

## Non-VM Setup

### Install `stitch` dependencies

The only `stitch` dependency is `rust` (tested on 1.64.0; default installation settings) which can be installed from: https://www.rust-lang.org/tools/install

For data analysis in evaluating the claims in the artifact, the following dependencies are needed:

Ensure that `python3` points to an installation of Python (tested on Python 3.8.10 but most likely 3.7+ will work). Ensure that the following dependencies are install for Python:
```
python3 -m pip install numpy matplotlib pandas seaborn prettytable
```
(tested with `numpy==1.23.3 matplotlib==3.6.0 pandas==1.5.0 seaborn==0.12.0 prettytable==3.4.1`)

Error we encountered: If you encounter the error `Error: pandas 1.5.0 has requirement python-dateutil>=...` then rerunning `python3 -m pip install pandas` resolved this.

Ensure that GNU time (i.e. `/usr/bin/time`) is installed - note that this is different from the `time` bash builtin. Check that running `/usr/bin/time` prints a "Usage:" explanation.

### Install the `stitch` artifact

Unzip `stitch-artifact.zip` from the [Zenodo link](https://zenodo.org/record/7150898) into a new folder `stitch-artifact`,  or alternatively clone `stitch-artifact` from github as follows:
```
git clone --recursive  https://github.com/mlb2251/stitch-artifact.git
```
If you did a normal clone without `--recursive`, run `git submodules update --init --recursive` to update the submodules.

### Install PyStitch dependencies (Claim 5 only)
PyStitch is a prototype DreamCoder-Stitch hybrid only used in Claim 5. If any issues are encountered running it outside of a VM, we recommend using a VM for simplicity.

Running PyStitch on a fresh installation of Ubuntu 20.04 LTS requires the following dependencies:
```
sudo apt install python3-pip pypy3
pypy3 -m pip install frozendict dill
```

## Sanity-test (kick the tires)

The artifact is split over several directories depending on the claim. Each command will be preceded by a `cd` to indicate where it is being executed, but as a quick reference here is the structure:
- `stitch` (Claim 1)
- `stitch_experiments` (Claims 2-3)
- `stitch_ablations` (Claim 4)
- `pystitch` (Claim 5)

Note that `stitch` corresponds to (a commit of) the main branch of the official Stitch repo `github.com/mlb2251/stitch` while `stitch_experiments` and `stitch_ablations` are adapted versions to support the additional analyses / ablations of the paper.

### Kicking Stitch's tires

Build and test `stitch` with the commands below. Downloading packages and compilation will each take a handful of minutes and will constitute the bulk of the runtime, while the tests themselves will likely take less than a minute (Our test runtimes: 24s in VM; 7.4s outside of VM).

```
cd stitch-artifact/stitch
make build
make test
cd ../stitch_experiments
make build
cd ../stitch_ablations
make build
```
All builds and tests should run without error (there may be some compiler warnings on the later build commands).

To check that all table/figure generation results work we will now step through each claim, running only the visualization step on the pre-computed results. **How to check that each visualization aligns with the paper is explained here (in addition to later in the full Evaluation instructions) but is not necessary here unless desired - the main point is to make sure there aren't any obvious data analysis crashes to catch and that plotting software works.**

### Claim 1
```
cd stitch-artifact/stitch/experiments
make claim-1-viz
```
This should run without error and re-generate the following PDFs from the paper:
- Fig 6: `stitch-artifact/stitch/experiments/plots/benches_compression_ratio_min.pdf`
- Fig 7: `stitch-artifact/stitch/experiments/plots/benches_mem_peak_kb.pdf`
- Fig 8: `stitch-artifact/stitch/experiments/plots/benches_time_per_inv_with_rewrite.pdf`

### Claim 2
```
cd stitch-artifact/stitch_experiments/experiments
export NUM_SEEDS=50
make claim-2-viz
```
This should run without error (don't forget to run the `export` statement first) and should generate and print out Table 2 from the paper. As discussed in the section `Validating Claim 2` below the Runtimes will be ~2x faster than the original results because of codebase improvements.

### Claim 3
```
cd stitch-artifact/stitch_experiments/experiments
make claim-3-viz
```
Fig 9: View the generated plot at `stitch-artifact/stitch_experiments/experiments/claim-3.pdf`. This should be identical to the one in the paper.

### Claim 4
```
cd stitch-artifact/stitch_ablations/experiments
make claim-4-viz
```

How to validate these results (copied from "Validating Claim 4 with pre-run results" section below):
> Table 3: Running make claim-4-viz should have printed out Table 3 from the paper. This is expected to be deterministic as it uses the number of nodes expanded instead of the runtime. Note however that there may be nondeterminism in whether TIME or MEM is the reason for failure - in particular a faster machine may be able to use up more memory before timing out and thus a TIME may become a MEM. We find in our re-running results that are identical to the paper table except for MEM in the top leftmost cell of the table.

### Claim 5
```
cd stitch-artifact/pystitch
./analyze.sh
```

This should run without error. See full description of analyzing these results in "Validating Claim 5 with pre-run results" below, which is slightly more involved than the other analyses - for the purposes of kicking the tires just running without error should suffice.

### Kicking PyStitch's tires

PyStitch is a prototype DreamCoder-Stitch hybrid used only in Claim 5. As we discuss in the section for evaluating Claim 5, some of these runs can be memory intensive and full logs from past runs are provided as an alternative means of verification. We also highly recommend a VM if errors are encountered given that this part of the artifact is intended to be a prototype.

The following command tests out PyStitch (Runtime: 4.5 min; Memory: 1.5GB)
```
cd stitch-artifact/pystitch
make test-1
```

There will be a lot of output ("WARNING"s are okay), and the end of the output should be of the form:
```
new primitives:
list(t0) -> (list(t0) -> t1) -> (t1 -> list(t0)) -> list(t0) 	 #(lambda (lambda (lambda (#(lambda (lambda (lambda (if (empty? $0) empty (cons $1 $2))))) ($0 ($1 (cdr $2))) (car $2) $2))))
int -> int 	 #(- 0)
int -> int 	 #(+ 1)
(t0 -> t1) -> list(t0) -> list(t1) 	 #(lambda (#(lambda (lambda (fix1 $0 $1))) (lambda (lambda (#(lambda (lambda (lambda (if (empty? $0) empty (cons $1 $2))))) ($1 (cdr $0)) ($2 (car $0)) $0)))))
(t0 -> t1 -> t1) -> t1 -> list(t0) -> t1 	 #(lambda (lambda (#(lambda (lambda (fix1 $0 $1))) (lambda (lambda (if (empty? $0) $2 ($3 (car $0) ($1 (cdr $0)))))))))
(int -> int -> int) -> (int -> t0) -> int -> list(t0) 	 #(lambda (lambda (#(lambda (lambda (fix1 $0 $1))) (lambda (lambda (if (eq? 0 $0) empty (cons ($2 $0) ($1 ($3 $0 1)))))))))
((t0 -> t1) -> t0 -> t1) -> t0 -> t1 	 #(lambda (lambda (fix1 $0 $1)))
list(t0) -> t0 -> list(t1) -> list(t0) 	 #(lambda (lambda (lambda (if (empty? $0) empty (cons $1 $2)))))
202.89716243743896 seconds
```

Full output from a similar run is in `stitch-artifact/pystitch/artifact_out/base-0.stderr` for reference but in particular it should end with a time printout (without crashing). The output need not match exactly.

# Evaluation instructions

## Claim 1

### Running Claim 1
```
cd stitch-artifact/stitch/experiments
make claim-1
```

- Runtime requirement: < 1 min (Ours took 34s)
- Memory requirement: low (Peak 200MB)
- Expected output: This will produce a large amount of output quickly as many stitch runs are launched serially. A full expected output log is given in `experiments/make-claim-1.log`. Note that timing/memory information has been manually appended to this log file for reference.
- Comments:
  - expected plots and other outputs are prepopulated in the artifact, however `make claim-1` will clear these outputs before re-running.
  - `make claim-1-viz` will execute just the graphing code for `claim-1` in case you encounter an error in the Python error and would like to re-run just that part of the code.

### Validating Claim 1

View the following plots in `stitch/experiments/plots` to verify that they align with those in the paper. Be mindful to get the exact file name, as some additional similarly named plots are also generated:
- Fig 6: `benches_compression_ratio_min.pdf`
- Fig 7: `benches_mem_peak_kb.pdf`
- Fig 8: `benches_time_per_inv_with_rewrite.pdf`

Variation in results: we expect that on some machines Fig 3 (wall-clock time) may be slower when run in a VM.

## Claim 2
Claim 2 involves running 50 random seeds and averaging over the results. Each seed takes about 3 minutes to run, so the full runtime is around 150 minutes. We give instructions below for configuring the number of seeds to run as well as providing logs for the full 50 seed run.

### Running Claim 2

First we will run with averaging over only 3 random seeds. From the root of the stitch-artifact repo, run the following (note the different `cd` path):
```
cd stitch-artifact/stitch_experiments/experiments
export NUM_SEEDS=3
make claim-2
```

In the paper we provide means and standard deviations across many seeds - in particular we ran it with 50 seeds. To recreate this you can repeat the above procedure but replacing `export NUM_SEEDS=1` with `export NUM_SEEDS=50` or whatever number of repetitions is desired.

- Runtime requirement: 3 min * NUM_SEEDS (i.e. ranges from 9 min to 150 min).
- Memory requirement: 1 GB (expected peak at around 800MB).
- Expected output: Full expected output logs for 3 seeds and 50 seeds are given in `experiments/make-claim-2-3.log` and `experiments/make-claim-2-50.log` respectively. Note that timing/memory information has been manually appended to these log files for reference.
- Comments:
  - expected plots and other outputs are prepopulated in the artifact, however `make claim-2` will clear these outputs before re-running.
  - `make claim-2-viz` will execute just the table-making code for `claim-2` in case you encounter an error in the Python error and would like to re-run just that part of the code.

### Validating Claim 2

- Table 2: running the `make claim-2` command above will generate and print Table 2 from the paper. Due to improvements in the codebase, the `Runtime` column might be even faster (~2x) than the results in the paper table and we will be updating the camera ready with these better results. Note that VM performance may vary.

## Claim 3

### Running Claim 3

From the root of the stitch-artifact repo, run:
```
cd stitch-artifact/stitch_experiments/experiments
make claim-3
```

- Runtime requirement: ~1 min (Ours took 57s)
- Memory requirement: Peak 463MB
- Expected output: A full expected output log is given in `experiments/make-claim-3.log`.  Note that timing/memory information has been manually appended to this log file for reference.
- Comments:
  - expected plots and other outputs are prepopulated in the artifact, however `make claim-3` will clear these outputs before re-running.
  - `make claim-3-viz` will execute just the graphing code for `claim-3` in case you encounter an error in the Python error and would like to re-run just that part of the code.

### Validating Claim 3

- Fig 9: View the generated plot at `experiments/claim-3.pdf`. This should be identical to the one in the paper.

## Claim 4

This claim is the ablation study. The ablation study takes about 20 hours to re-run and requires 50GB of RAM. We provide instructions on how to re-run it, but also full logs from running it as an alternate form of artifact if preferred.

### Validating Claim 4 with pre-run results

Pre-run files and logs:
- `stitch-artifact/stitch_ablations/experiments/` is the parent folder for all the following files (note the difference from earlier paths in this guide).
- `claim-4-out` contains the output from running the ablation study. This includes subfolder `stdout` with the logs of what stitch printed during each ablation run, as well as the subfolder `raw` which includes the jsons that stitch outputted with results from each run.
- `expected-claim-4-out` is a backup of `claim-4-out` (in case `make clean` or `make claim-4` is run which would overwrite the other copy).
- `make-claim-4-viz.log` is a log of the output from running `make claim-4-viz`.
- `make-claim-4.log` is a log of the output from running `make claim-4`.  Note that timing/memory information has been manually appended to this log file for reference.

Generating table from pre-run files:
```
cd stitch-artifact/stitch_ablations/experiments
make claim-4-viz
```

Validating these results:
- Table 3: Running `make claim-4-viz` should have printed out Table 3 from the paper. This is expected to be deterministic as it uses the number of nodes expanded instead of the runtime. Note however that there may be nondeterminism in whether TIME or MEM is the reason for failure - in particular a faster machine may be able to use up more memory before timing out and thus a TIME may become a MEM. We find in our re-running results that are identical to the paper table except for MEM in the top leftmost cell of the table.

### Running Claim 4 (expensive)

This step will take ~20 hours and 50GB RAM.

See "Pre-run files and logs" above for all expected outputs from this step, which may serve as an alternative if preferred.

From the root of the stitch-artifact repo, run:
```
cd stitch-artifact/stitch_ablations/experiments
make claim-4
```

This will overwrite the pre-run files. Proceed to the same data analysis used during pre-run to verify results.

## Claim 5

### Validating Claim 5 with pre-run results

Pre-run files and logs:
- `stitch-artifact/pystitch/` is the parent folder for all the following files (note the difference from earlier paths in this guide).
- `artifact_out` contains all the output data generated from running `./claim-5.sh`. This includes one file per pystitch run, all of the form `$model-$iteration.stderr` where `$model` is `base`, `bayes`, `vs`, or `bayes_vs` corresponding to the 4 rows of Table 4, and iterations range from `0` to `3` and correspond to the different historical DreamCoder iterations that the DreamCoder log files came from which are input to this. In particular, we expect to find Fold Unfold and Map at iteration 0, Filter on iteration 1, and ZipWith on iteration 3.
- `expected_artifact_out` is a backup of `artifact_out` (in case `make clean` is run which would delete the other copy).
- `claim-5.log` is a log of the console output from running `./claim-5.sh`.
- `analyze.log` is a log of the console output from running `./analyze.sh`.

First we note some revisions to Table 4. A small bug in the prototype was fixed which allows for finding `MAP` and `FOLD` at earlier iterations and there is no longer a "half credit" `FILTER`, while increasing runtimes (still remaining 75x faster than DreamCoder). The revised Table 4 is given below (Y/N instead of check and X):
```
         Fold  Unfold Map  Filter ZipWith  Time
Base:      Y     N     Y     N     Y       503
+Bayes:    Y     N     Y     N     Y       817
+VS:       Y     Y     Y     N     Y       3231
+Bayes+VS: Y     Y     Y     Y     Y       3042
```


Run the analysis on the pre-run data like so:
```
cd stitch-artifact/pystitch
./analyze.sh
```

The output should look like the output in `analyze.log`. In particular, `./analyze.sh` script is looking through the logs in `artifact_out/` and pulling out the relevant functions for this analysis. For each row of the table there will be a list of outputs, for example the first row is the "base" model:

```
Model: base
Runtime: 576.12354826927185
FOLD
(t0 -> t1 -> t1) -> t1 -> list(t0) -> t1         #(lambda (lambda (#(lambda (lambda (fix1 $0 $1))) (lambda (lambda (if (empty? $0) $2 ($3 (car $0) ($1 (cdr $0)))))))))
UNFOLD
MAP
(t0 -> t1) -> list(t0) -> list(t1)       #(lambda (#(lambda (lambda (fix1 $0 $1))) (lambda (lambda (#(lambda (lambda (lambda (if (empty? $0) empty (cons $1 $2))))) ($1 (cdr $0)) ($2 (car $0)) $0)))))
FILTER
ZIP
(t0 -> t1 -> t2) -> list(t0) -> list(t1) -> list(t2)     [very long program omitted]
```

From the above log, the runtime is given by the line:
```
Runtime: 576.12354826927185
```

Which should be similar (modulo VM performance) to the runtime in the revised Table 4 given above.

This is followed by a printout of each function `FOLD` `UNFOLD` `MAP` `FILTER` and `ZIP`, where if a function with a matching type signature has been found it is printed on the line following it. So in the above example, `FOLD` `MAP` and `ZIP` were found but `UNFOLD` and `FILTER` were not. We hand-checked the functions in `analyze.log` for correctness.

Looking at the expected output table below we see that the desired output agrees with the outputs we got above:
```
         Fold  Unfold Map  Filter ZipWith  Time
Base:      Y     N     Y     N     Y       503
```

This procedure can be repeated for the next 3 rows of the table.

### Re-running Claim 5

To re-run these results requires 20GB of RAM and ~2 hrs (may vary with VM performance), hence the alternative of log files from a previous run given above.

From the root of the stitch-artifact repo, run:
```
cd stitch-artifact/pystitch
make clean
./claim-5.sh
```

The results will be generated in `artifact_out/` and can be validated in the same way as the pre-run results.

## Appendix: Rerunning DreamCoder Baselines (expensive)

This section covers how to re-run the DreamCoder baselines from Claim 1. This relies on DreamCoder's pre-built compression binary, which runs on the VM (and may work on other Linux machines). If you aren't already in the VM you'll need an extra dependency for this (relative to a fresh Ubuntu 20.04):
```
sudo apt install libzmq3-dev
```

To re-run everything, which requires 10GB memory and takes ~1 day on 8 cores (mileage may vary depending on VM performance):
```
cd stitch-artifact/stitch/experiments
make dreamcoder
```
The complete results of this are already pre-populated at `stitch-artifact/stitch/experiments/compression_benchmark` and have been available at https://github.com/mlb2251/compression_benchmark as linked from the original paper.

# Additional artifact description

## Organization of Artifact

This artifact is a clone of the [stitch-artifact](https://github.com/mlb2251/stitch-artifact/) GitHub repository. There are 4 directories at the base of this artifact:
- `stitch` (Claim 1 & Trying out on your own inputs)
  - corresponds to (a commit of) the main branch of the [official Stitch repo](github.com/mlb2251/stitch)
- `stitch_experiments` (Claims 2-3)
  - adapted to support the additional analyses of the paper.
- `stitch_ablations` (Claim 4)
  - adapted to support the ablations and analyses of the paper.
- `pystitch` (Claim 5)
  - the PyStitch prototype from Section 6.5 of the paper.

Structure of `stitch` subdirectory (i.e. the official stitch repo):
- `data` - contains json-format data used as input to stitch. `data/cogsci` contains the data used in Claims 2-4 of the paper while `data/basic` contains a inputs to test various functionality.
- `examples/stitch.rs` - this is used to build the Python bindings for Stitch (not used in this artifact)
- `experiments` this is where anything related to running paper experiments goes - more details given in `stitch/experiments` section below.
  - `compression_benchmark` this is a clone of https://github.com/mlb2251/compression_benchmark which contains the DreamCoder benchmarks that we compare against in Claim 1. The README of that GitHub describes the format of the benchmark.
  - `plots` is where the plots of Claim 1 are rendered
  - `Makefile` - this contains commands used during the evaluation like `make claim-1` and `make claim-1-viz` (in `stitch_experiments/experiments` this file will contain commands for `claim-2` and `claim-3`, and in `stitch_ablations/experiments` this file will contain commands for `claim-4`).
  - `analyze.py` this script does much of the data analysis work and is frequently invoked in different modes by the Makefile or bash scripts.
  - `bench_dreamcoder.sh` this is used to run a single benchmark of DreamCoder on `compression_benchmark`.
  - `bench_dreamcoder_all.sh` this is used to run ALL benchmarks in `compression_benchmark` by repeatedly invoking `bench_dreamcoder.sh`. This is called by `make dreamcoder`.
  - `bench_stitch.sh` This is used for launching a single Stitch run to benchmark it against a DreamCoder run.
  - `bench_stitch_all_latest.sh` This automates finding all the most recently generated DreamCoder runs and running Stitch against each of them
  - `compression` This is a precompiled DreamCoder binary for running DreamCoder's abstraction learning, used by `bench_dreamcoder.sh`.
  - `process_all.sh` This does some post-processing of stitch and dreamcoder runs that were launched by `bench_stitch.sh` and `bench_dreamcoder.sh`.
- `src` this is the source of stitch
  - `bin/compress.rs` this is the entrypoint for stitch when you run it in the usual manner, with `cargo run --release --bin=compress`
  - `compression.rs` this is the main file in the Stitch codebase, where all the abstraction learning (i.e. "compression" happens)
  - `domain.rs` this isn't used in this paper, but defines an interface for giving execution semantics to programs (see also `src/domains/` for example domains)
  - `expr.rs` this defines expressions (the `Expr` struct) used throughout `stitch`, as well as the `Lambda` struct corresponding to a node in an expression, designed to be compatible with the `egg` library (by implementing `Language`).
  - `extraction.rs` this is called by `compression.rs` and does the work of rewriting a program using an abstraction
  - `formats.rs` this defines various input formats for Stitch
  - `lib.rs` this is the toplevel library file for Stitch, it mainly just links all the other files togehter.
  - `macros.rs` this provides helpful macros, though they're largely used by the `domain.rs` semantics extension of Stitch that isn't used in this paper.
  - `util.rs` various utilities
- `tests/integration_tests.rs` These are the integration tests run by `make test`. They use cached expected outputs across many simple and complex benchmarks and are automatically run by a GitHub Action to check for regressions.
- `Makefile`
  - `make build` this runs `cargo build --release --bin=compress` which builds stitch
  - `make test` this runs `cargo test --release --test integration_tests` which runs the tests in `stitch/tests/integration_tests.rs`

`stitch_experiments` and `stitch_ablations` mirror the structure of `stitch`. *All log files relevant to specific claims are described in the section pertaining to that claim instead of here.*

## Running on your own inputs

Go into the `stitch-artifact/stitch` subdirectory of the Artifact, where the main implementation of `stitch` lives.

Lets take a look at some simple examples of the `stitch` input format. Put the following in a new file `data/basic/ex1.json`:
```json
[
    "(foo (a a a))",
    "(bar (b b b))"
]
```
As above, stitch input format is a json file containing a list of input programs, where each program is a string written in a lisp-like lambda calculus syntax. The first program in this example corresponds to the curried lambda calculus expression `(app foo (app (app a a) a))`.

The clear structure in these examples is that they all contain a subterm of the form `\x. (x x x)`. Lets see if stitch can pull that out:

```
cargo run --release --bin=compress -- data/basic/ex1.json --max-arity=3 --iterations=1
```

(If you're having any trouble, check out other examples like `data/basic/simple1.json` to make sure you have the right format.)

The output should look like:
```
=======Compression Summary=======
Found 1 inventions
Cost Improvement: (1.33x better) 806 -> 604
fn_0 (1.33x wrt orig): utility: 200 | final_cost: 604 | 1.33x | uses: 2 | body: [fn_0 arity=1: (#0 #0 #0)]
Time: 0ms
Wrote to "out/out.json"
```

Quick primer on the output format:
- `Cost Improvement: (1.33x better) 806 -> 604`
  - here we see that by the cost metric (which values terminals like `foo` and `a` as `100` and nonterminals like `app` and `lam` as `1`) our programs were rewritten to be 1.33x smaller. To see the actual rewritten programs you can include `--show-rewritten` in the command and the rewritten programs will appear a few lines above the compression summary:
    - `(foo (fn_0 a))` and `(bar (fn_0 b))`
- `fn_0`
  - this is the name the new primitive was assigned
- `(1.33x wrt orig)`
  - this is a *cumulative* measure of compression (ie "with respect to original"), so if we were learning more than one abstraction it would represent the accumulated compression over all previous abstractions
- `utility: 200`
  - This is the utility, which corresponds to the difference in program cost before and after rewriting.
- `final_cost: 604`
  - final cost of programs after rewriting
- `1.33x`
  - compression gained from this abstraction - again, only relevant when learning more than one abstraction
- `uses: 2`
  - the abstraction is used twice in the set of programs
- `body: [fn_0 arity=1: (#0 #0 #0)]`
  - this is the abstraction itself! `(#0 #0 #0)` is equivalent to `\x. (x x x)` - the first abstraction variable is always `#0`, the second is `#1`, etc.
  - Theres also a more complete output that is sent to `out/out.json` by default and can be consumed by other programs that are using stitch as a subroutine (if they arent using the Rust/Python bindings for it).

Now that you've had a primer lets take a look at the output of one of the benchmarks from the paper. This will be the `data/cogsci/nuts-bolts.json` file from the [Wong et al. 2022] dataset, feel free to open the file and take a look.

Now lets run it, this time using `--iterations=3` to get 3 abstractions:
```
cargo run --release --bin=compress -- data/cogsci/nuts-bolts.json --max-arity=3 --iterations=3
```

The output should end with the following compression summary:
```
=======Compression Summary=======
Found 3 inventions
Cost Improvement: (6.06x better) 1919558 -> 316890
fn_0 (1.78x wrt orig): utility: 837792 | final_cost: 1079238 | 1.78x | uses: 320 | body: [fn_0 arity=2: (T (repeat (T l (M 1 0 -0.5 (/ 0.5 (tan (/ pi #1))))) #1 (M 1 (/ (* 2 pi) #1) 0 0)) (M #0 0 0 0))]
fn_1 (3.81x wrt orig): utility: 572767 | final_cost: 503538 | 2.14x | uses: 190 | body: [fn_1 arity=3: (repeat (T (T #2 (M 0.5 0 0 0)) (M 1 0 (* #1 (cos (/ pi 4))) (* #1 (sin (/ pi 4))))) #0 (M 1 (/ (* 2 pi) #0) 0 0))]
fn_2 (6.06x wrt orig): utility: 185436 | final_cost: 316890 | 1.59x | uses: 168 | body: [fn_2 arity=1: (T (T c (M 2 0 0 0)) (M #0 0 0 0))]
Time: 120ms
Wrote to "out/out.json"
```

These are written in a low-level graphics DSL, and the first function (which yields 1.78x compression) is the function for rendering a scaled n-sided polygon, which is used 320 times in the dataset.

Quick primer on input format:
- Variables should be written as *de Bruijn* indices (i.e. `$i` refers to the variable bound by the `i`th lambda above it) so `\x. \y. x y` is written `(lam (lam ($1 $0)))`
- Lambdas need explicit parentheses around their body so `(lam + 3 2)` should instead be written `(lam (+ 3 2))`. The parser outputs an error message explaining this if you make this mistake.
- There should be parentheses around the whole program at the outermost level so `lam (+ 3 2)` should instead be written `(lam (+ 3 2))` and `+ foo bar` should be written `(+ foo bar)`. The parser will otherwise output an error about `unrecognized post-s-expression data`.
- Be sure to balance your parentheses or you'll get an error about `unexpected eof` or `unrecognized post-s-expression data`.
- You don't need to pre-define a DSL or anything to work with stitch. Any space-separated series of tokens that isn't reserved for something else is treated as a DSL primitive, like `foo` and `a` in the earlier example or any of the primitive likes `T` or `-0.5` in the second example.
- check out other examples in `data/basic` and `data/cogsci`

You can also see a full list of command-line arguments you can explore by running `cargo run --release --bin=compress -- --help`

There's also a bit more information in the readme at the [official github](https://github.com/mlb2251/stitch) including some instructions for running the Python bindings (however these are in development and haven't been widely tested yet).

Feel free to modify examples and play around! A more complete tutorial will come out when we more officially publicize `stitch`, but I hope this was a helpful primer, and thank you for reviewing - we appreciate the time and feedback!

