# Regular flips in mptopcom

This repository contains supplementary material for the paper [[1]](#1).

The file `D5xD2_triang_with_flip_matrix.txt` contains the triangulation giving
rise to the matrix of Figure 2.

The folder `examples` contains the input files for the tables 1 and 2.

The folder `cluster_scripts` contains cluster scripts that produced the
runtimes of table 2.

## Cluster scripts
There are two cluster scripts that are derived from the main benchmarking
script used for mptopcom `regular_benchmark.job` and
`regular_benchmark_st.job`. The `st` just stands for "single threaded".
Both scripts are intended for the [slurm workload
manager](https://slurm.schedmd.com/overview.html) and can be run with the
command `sbatch regular_benchmark(_st).job`.

## Valgrind
We used the `valgrind` command to profile `mptopcom` 1.2, 1.3, and 1.4. We ran
all three versions for ten minutes using `timeout` and added the output to the
folder `valgrind` of this repository. The profiles can be viewed using a tool
like `kcachegrind`.

The commands (without local paths) we used were
```
timeout 10m valgrind --tool=callgrind mptopcom-1.2/bin/mptopcom1 --regular --flip-cache 2000 --orbit-cache 2000 --regularity-cache 2000 < cube_4.dat
timeout 10m valgrind --tool=callgrind mptopcom-1.3/bin/mptopcom1 --regular --flip-cache 2000 --orbit-cache 2000 --regularity-cache 2000 < cube_4.dat
timeout 10m valgrind --tool=callgrind mptopcom-1.4/bin/mptopcom1 --regular --flip-cache 2000 --orbit-cache 2000 --regularity-cache 2000 < cube_4.dat
```
During this run, `mptopcom` 1.2 found 100 regular triangulations, mptopcom 1.3
found 107, and mptopcom 1.4 found 1770. Note that running anything in a profiler
will make it much slower, and this slow-down may not preserve runtime relations
observed before.


## References
<a id="1">[1]</a>
Lars Kastner: Regular Flips in mptopcom
