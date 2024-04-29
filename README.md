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

There are seveal interesting blocks one can have a look at. The first one is
```
RegularityCheckCache::isRegular
```
which exists in two variants. The first one for mptopcom 1.2 just takes a
`TriangNode` as input, while the second in mptopcom 1.3 takes essentially an
entire flip as input. These both consume about 88% of the runtime, however keep
in mind that mptopcom 1.3 needs to solve many more linear programs, since there
usually are more flips than triangulations. When run outside of valgrind,
mptopcom 1.3 is significantly faster than mptopcom 1.2, roughly 5-10 times.
This block does not exist in mptopcom 1.4, as we are treating all flips of a
triangulation at the same time.

The second block one can look at is
```
polymake::polytope::soplex_interface::Solver::solve
```
measuring how much time is spent in the soplex interface. This is 32% for
mptopcom 1.2, 79% for mptopcom 1.3, and 1,07% for mptopcom 1.4. From this the
it already becomes clear that mptopcom 1.4 spends much less time solving linear
programs than mptopcom 1.2 and 1.3. However the difference between 1.2 and 1.3
needs some explanation. There are two reasons for this odd difference: First,
as mentioned, mptopcom 1.3 has to solve many more linear programs than 1.2,
even though these linear programs are smaller. Second, mptopcom 1.2 has to
assemble much larger linear programs for checking whether a triangulation is
regular. This assembly process is denoted as the 50% block with
`std::pair<pm::SparseMatrix<long> [...]>`, which calls polymakes
`secondary_cone_ineq` function.


## References
<a id="1">[1]</a>
Lars Kastner: Regular Flips in mptopcom
