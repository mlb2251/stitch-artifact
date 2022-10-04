

# List of Claims
- Claim 1: Stitch learns libraries of comparable quality to those found by existing deductive library learning algorithms in prior work, while requiring significantly less resources
- Claim 2: Stitch scales to corpora of programs that contain more and longer programs than would be tractable with prior work.
- Claim 3: Stitch degrades gracefully when resource-constrained.
- Claim 4: All the elements of Stitch matter.
- Claim 5: Stitch is complementary to deductive rewrite-based approaches to library learning. This is supported on a theory level by Sec 5, while in Sec 6.5 we present the prototype "PyStitch" as a Stitch-DreamCoder hybrid to "give evidence that it is possible to make Stitch recover all of these higher-order functions by layering it on top of the version space obtained after a single step of DreamCoder’s 𝛽-reduction inversion".

# Download, installation, and sanity-testing instructions


## Prerequisites

If you use the VM, none of the following prerequisites are necessary

### Stitch prerequisites

Install rust (tested on 1.64.0; default installation settings) https://www.rust-lang.org/tools/install which currently requires running:

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`


### Results analysis prerequisites
Ensure that `python3` points to python (tested on Python 3.8.10 but most likely >=3.7 would work), and ensure that the following dependencies are installed:
```
python3 -m pip install numpy matplotlib pandas seaborn prettytable
```

Ensure that `/usr/bin/time` is installed - note that this is different from the `time` bash builtin. Check that running `/usr/bin/time` prints a "Usage:" explanation.

## Download stitch
```
git clone https://github.com/mlb2251/stitch.git
cd stitch
git checkout artifact-main
export STITCH_DIR=$PWD
```
The benchmark repo should be cloned *inside* of the stitch directory, as the instructions above imply.

You may want to add `export STITCH_DIR=path/to/stitch` to your `.bashrc` as this is used by many analysis scripts.

## Download PyStitch
**PyStitch is only used in Claim 5, and while Stitch itself is likely easy to run on any machine if the dependencies can be installed, PyStitch inherits many DreamCoder dependencies that make it far more difficult to run - so we highly recommend using a VM for Claim 5 instead of installing PyStitch yourself**.

While Stitch will be used for most of our experiments, as per the paper PyStitch is a "prototype" implementation of Stitch merged with DreamCoder to "give evidence that it is possible to make Stitch recover almost all of these higher-order functions by layering it on top of version space obtained after a single step of
DreamCoder’s 𝛽-reduction-inverting refactoring". 

PyStitch lives in the DreamCoder repo which is large (~4GB) so this clone will take a while:
```
git clone -b pystitch-artifact https://github.com/ellisk42/ec.git pystitch
cd pystitch
git submodule update --recursive --init
```

Running PyStitch requires pypy3
```
sudo add-apt-repository ppa:pypy/ppa
sudo apt update
sudo apt install pypy3
sudo apt-get install python3-pip

pypy3 -m pip install frozendict dill



```


# Evaluation instructions

## Kick the Tires
Expected time: <10 min.

Build and test `stitch` with the commands below. Downloading packages and compilation will each take a handful of minutes and will be the bulk of the runtime, while the tests themselves will likely take less than a minute (Our test runtimes: 24s in VM; 7.4s outside of VM).

```
make build
make test
```



Try out PyStitch (Runtime: 4.5 min; Memory: 1.5G)
```
pypy3 bin/sasquatch.py -v -a 0 -i -t --iteration 0
```

Try out PyStitch with rewrites
```
pypy3 bin/sasquatch.py -v -a 1 -i -t --iteration 0
```



## Claim 1
Expected time: <10 min.

From the root of the stitch repo, run:
```
export STITCH_DIR=$PWD
git checkout artifact-main
cd experiments
git clone https://github.com/mlb2251/compression_benchmark.git
make claim-1
```

The `make claim-1` command will produce a huge amount of output and should end with `wrote to plots/benches_compression_ratio_min.png and pdf`

Results: View the relevant plots `stitch/experiments/plots` to verify that they align with those in the paper (be mindful to get the exact file name, as some additional similarly named plots are also generated):
- Fig 1: `benches_compression_ratio_min.pdf`
- Fig 2: `benches_mem_peak_kb.pdf`
- Fig 3: `benches_time_per_inv_with_rewrite.pdf`

We expect that on some machines Fig 3 (wall-clock time) may be slower when run in a VM, though in our tests this didn't seem to be an issue.

## Claim 2
Expected time: ~2 min per run; paper performs 50 runs but below we first give the instruction for a single run while providing the logs for all 50 runs as well as instructions on how to run them if desired (~100 min).


First we will run with only a single random seed. From the root of the stitch repo, run:
```
export STITCH_DIR=$PWD
git checkout artifact-experiments
cd experiments
export NUM_SEEDS=1
make claim-2
```
This will take about 2 minutes to complete.

In the paper we provide means and standard deviations across many seeds - in particular we ran it with 50 seeds. Repeat the above procedure but replacing `export NUM_SEEDS=1` with a higher number of seeds as desired.


Results:
- Table 2: running the `make claim-2` command above will print out the contents of Table 2 from the paper. Due to improvements in the codebase, the Runtime column might be even faster (~2x) than the results in the paper table and we will be updating the camera ready with these better results. However, also VM performance may impact results here.

## Claim 3
Runtime: <10 min

From the root of the stitch repo, run:
```
git checkout artifact-experiments
cd experiments
make claim-3
```

Results:
- Fig 9: View the generated plot at `stitch/experiments/claim-3.pdf`. This should be identical to the one in the paper.

## Claim 4
Runtime: ~20 hours to re-run the ablation study and requires 50GB of RAM; we provide full logs from running it ourselves as well as instructions on how to re-run it.
From the root of the stitch repo, run:
```
export STITCH_DIR=$PWD
git checkout artifact-ablation_experiments
cd experiments
make claim-4
```

## Claim 5

```
export STITCH_DIR=$PWD
git checkout
cd experiments
make claim-5
```




# Additional artifact description







# For reference, we provide the steps it took for us to get the Ubuntu 20.04 LTS running Stitch

Install git with
`sudo apt install git`
`sudo apt install curl`
`sudo apt install python3-pip`


