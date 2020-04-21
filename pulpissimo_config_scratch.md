# Download Software and Configure the Environment from scratch

## Setup the Fetch Chain
To use the pulp-runtime and run some tests, it is necessary to set up a working PULPissimo system. Moreover, to modify the platform (both HW and SW) it is necessary to fetch code from personal forks and eventually branches. 
Following examples use forks for user `abettati` and eventually modify files on the `CLINTXpulp` branch.

```bash
# clone abettati/pulpissimo repo
git clone git@github.com:abettati/pulpissimo.git
cd pulpissimo

# Setup new local branch to track remote branch CLINTXpulp
# this way any push will be directed to that branch
git checkout --track origin/CLINTXpulp
```
To build the PULPissimo system in a personalized way, the right IPs must be fetched from the respective repos. Meaning that from `pulpissimo`, the correct `pulp_soc` repo (`abettati/pulp_soc`) and branch (`CLINTXpulp`) must be fetched. Thus the `pulpissimo/ips_list.yml` file (in branch `CLINTXpulp` of `abettati/pulpissimo`) needs to be modified to include:
```bash
pulp_soc:
  commit: CLINTXpulp
  server: https://github.com
  group:  abettati
```
Similarly, the `CLINTXpulp` branch in `abettati/pulp_soc` must point to the `CLINTXpulp` branch in the ` abettati/cv32e40p` for the final PULPissimo system to use the desired version of the cv32e40p (RI5CY) core. This is forced by modifying the `pulp_soc/ips_list.yml` in the `CLINTXpulp` branche of the `abettati/pulp_soc` repo:

```bash
cv32e40p:
  commit: CLINTXpulp
  group: abettati
```
### Build and Configure PULPissimo
Move on to effectively build and configure the system.

```bash
# Fetch and update ips from github
./update-ips

# Make modelsim point to this instance of PULPissimo
source setup/vsim.sh

# build all up (warnings expected and ok)
cd sim/
make clean all
```
Notice: repos are now linked, so that I can push to `abettati/pulp_soc` from `abettati/pulpissimo/ips/pulp_soc`.

**ANGELO: QUI E' DOVE ho GLI ERRORI**
```
** Error (suppressible): ../ips/pulp_soc/rtl/fc/fc_subsystem.sv(185): (vopt-3013) A parameter override is not allowed for localparam 'N_EXT_PERF_COUNTERS'.
** Error (suppressible): ../ips/pulp_soc/rtl/fc/fc_subsystem.sv(185): (vopt-3013) A parameter override is not allowed for localparam 'PULP_SECURE'.
** Error (suppressible): ../ips/pulp_soc/rtl/fc/fc_subsystem.sv(185): (vopt-3013) A parameter override is not allowed for localparam 'FP_DIVSQRT'.
** Error (suppressible): ../ips/pulp_soc/rtl/fc/fc_subsystem.sv(185): (vopt-3013) A parameter override is not allowed for localparam 'SHARED_FP'.
** Error (suppressible): ../ips/pulp_soc/rtl/fc/fc_subsystem.sv(185): (vopt-3013) A parameter override is not allowed for localparam 'SHARED_FP_DIVSQRT'.
Optimization failed
```

### Install the PULP SDK
The SDK is needed in order to run some program on our PULPissimo platform. In this example, the SDK Release (`pulp-platform/pulp-sdk-release`) is used.

```bash
git clone git@github.com:pulp-platform/pulp-sdk-release.git

# Force the distribution to be used in build process
export PULP_ARTIFACTORY_DISTRIB=CentOS_7
cd pulp-sdk-release/

# list available sdk versions
make list

# get a specific version
make version=2020.01.01 get

# Configuring PULP SDK
source env/env-sdk-2020.01.01.sh

# list available targets
make targets

# Choose and source a target
source pkg/sdk/2020.01.01/configs/pulpissimo.sh

# List available platforms
make platforms

# Choose and source a platform
source pkg/sdk/2020.01.01/configs/platform-rtl.sh
```
### Configure `pulp-runtime`
`pulp-runtime` is a simple runtime for verification tests.

```bash
# clone the repo
git clone git@github.com:pulp-platform/pulp-runtime.git

# configure the environment to use it
source pulp-runtime/configs/pulpissimo.sh

# set compiler path
export PATH=/usr/pack/pulpsdk-1.0-kgf/artifactory/pulp-sdk-release/pkg/pulp_riscv_gcc/1.0.13/bin/:$PATH
```
This should set `VSIM_PATH=/home/bettatia/prj/abet/pulpissimo/sim.`

Some test to start working on are hosted on gitlab: [riscv-tests](https://iis-git.ee.ethz.ch/pulp-sw/riscv_tests).

```bash
git clone git@iis-git.ee.ethz.ch:pulp-sw/riscv_tests.git
```
(when prompted insert iis-git.ee.ethz.ch gitlab account pw) 
Then launch the test, e.g. testIRQ: 
```bash
cd riscv-tests/testIRQ
make clean all run gui=1
```

**ANGELO: Apre modelsim con alcuni errori**
```
# Start time: 17:02:24 on Apr 21,2020
# ** Note: (vsim-3812) Design is being optimized...
# ** Error (suppressible): (vopt-19) Failed to access library 'models_lib' at "models_lib".
# No such file or directory. (errno = ENOENT)
# ** Error (suppressible): (vopt-19) Failed to access library 'models_lib' at "models_lib".
# No such file or directory. (errno = ENOENT)
# ** Warning: (vopt-2669) Unable to find library 'models_lib'.
# ** Error: (vopt-13130) Failed to find design unit vopt_tb.
#         Searched libraries:
#             vip_lib
#             work
# Optimization failed
# Error loading design
# End time: 17:02:25 on Apr 21,2020, Elapsed time: 0:00:01
# Errors: 3, Warnings: 1
# Coverage command may be used in loaded simulation and in viewcov mode only.
#  
run -all
# No Design Loaded!
```
