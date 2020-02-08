# Run `pulp_tests` on PULPissimo using `pulp-runtime`
`pulp-runtime` is a runtime designed to run verification tests.

## Download and compile PULPissimo RTL

Setup pulpissimo.

```bash
# clone from github
cd ~/prj/
git clone https://github.com/pulp-platform/pulpissimo.git
cd pulpissimo/

# Fetch and update ips from github
./update-ips

# update modelsim path to the correct rtl platform
source setup/vsim.sh

# build all up (warnings expected and ok)
cd sim/
make clean all
```

## Download and configure `pulp-runtime`

```bash
# clone the repo from github
git clone git@github.com:pulp-platform/pulp-runtime.git

# configure the environment to use it
source pulp-runtime/configs/pulpissimo.cfg

# set compiler path
export PATH=/usr/pack/pulpsdk-1.0-kgf/artifactory/pulp-sdk-release/pkg/pulp_riscv_gcc/1.0.13/bin/:$PATH
```

## Download the tests
Some tests to start working on are hosted on GitLab (iis-git.ee.ethz.ch): 
[riscv_tests](https://iis-git.ee.ethz.ch/pulp-sw/riscv_tests) 

```bash
# clone from GitLab (when prompted insert iis-git.ee.ethz.ch gitlab account pw)
https://iis-git.ee.ethz.ch/pulp-sw/riscv_tests
```
Then launch the test, e.g. testIRQ:
```bash
cd riscv-tests/testIRQ
make clean all run
```