---
hide: 
    - navigation
---

On this page, you'll find quick fixes for the most common problems you may encounter. 

!!! tip "Your feedback matters!" 
    Ran into an issue that's not listed here? [Let us know!](mailto:maximilian.gram@uni-wuerzburg.de,tomgr@med.umich.edu)

## Reconstruction/Mapping

### Invalid MEX-file (GLIBC version not found)
This error can occur during the pre-simulation step for dictionary creation when MATLAB tries to load a precompiled .mexa64 binary and fails with a message like:
<pre style="white-space: pre-wrap; word-break: break-word;"><code>Error using MRF_sim_pre>pre_sim_adiabatic_pulses
Invalid MEX-file '.../OpenMRF/openmrf-core-matlab/include_pulseq_toolbox/src_mrf/src_simulations/src_mex/mex_EPG_rf_relax.mexa64': /lib64/libm.so.6: version `GLIBC_2.29' not found(required by .../OpenMRF/openmrf-core-matlab/include_pulseq_toolbox/src_mrf/src_simulations/src_mex/mex_EPG_rf_relax.mexa64)</code></pre>
#### Why it happens
The MEX binary was compiled on a system with a newer GNU C Library (glibc) than the one available on the machine where you are running MATLAB. On many HPC clusters (and older Linux installations), compute nodes may have an older glibc. Because glibc is not forward-compatible, a MEX file built against `GLIBC_2.29` will not load on systems with `glibc < 2.29`.

#### Fix: Recompile the MEX files on target system
Rebuild the MEX binaries on the same system (and ideally the same type of node) where you will run the simulations. OpenMRF provides a script to do this:

1. Start MATLAB on the machine/cluster where you will run the code.
Run: `openmrf-core-matlab/include_pulseq_toolbox/src_mrf/src_simulations/src_mex/compile_mex.m`
2. This recompiles the required `.mexa64` files against the local toolchain/glibc so they can be loaded successfully.
<!-- This error can occur during pre-simulation for dictionary creation. More specifically, you are likely trying to call a mex function on a system with `glibc<2.29`. Per default, all mex functions in the core matlab repository are compiled for glibc 2.29 since that's the predominant version on most standalone computers, so if you're encountering this problem, you're likely working on an old machine or a computing cluster. Luckily, there is an easy fix - just run the `compile_mex.m` script in `openmrf-core-matlab/include_pulseq_toolbox/src_mrf/src_simulations/src_mex/`. This should recompile all required mex binaries for your glibc version.  -->
