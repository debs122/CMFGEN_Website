# Running a Model

## Starting from an existing model

To begin with, let's rerun a model that was already prepared by John
Hillier. Pick the Ostar model, one of the three example models available
on the CMFGEN website — it's called `zpup_like_model`.

**a)** Prepare a new model directory in your preferred location:

```bash
mkdir new_model
```

**b)** CMFGEN needs a model to start from, so copy over the files that a
new model needs from the Ostar model you just downloaded, using `cpmod`:

```bash
cpmod Ostar_model new_model
```

`Ostar_model` refers to the location of the Ostar model you downloaded. If
this worked, the new directory should now be full of files.

Sometimes `cpmod` complains that the function `out2in` could not be
found. Since `out2in` prepares input files from output files, if it
didn't work you'll see many files ending in `OUT` in the new model
directory. In that case, enter the model directory and run `out2in`
yourself:

```bash
cd new_model
out2in
```

This should produce many files ending in `IN`.

**c)** Since we are just rerunning a model, we don't need to recompute the
hydrostatic structure. In the input file `VADAT`, switch off `DO_HYDRO`:

```
F     [DO_HYDRO]
```

Also double check that `LIN_INT` is set to `T`:

```
T     [LIN_INT]
```

`LIN_INT` determines whether populations should be interpolated from the
previous model. Since we're just re-running this model, it should be set
to `true`. When any physical parameter is changed, this needs to be set
to `false` — it can be set back to `true` if, for example, only the
number of meshpoints is increased.

While you're in `VADAT`, take a moment to look through the other
parameters there. It's one of the two main input files of CMFGEN and is
worth getting familiar with.

**d)** Now start the model. It will take some time and memory to run, so
make sure you have the CPUs that are parallelized over in `batch.sh` (see
the `OMP_NUM_THREADS` setting) and at least 10GB of RAM available.

Before you start, check the `batch.sh` file and whether the `local_path`
variable will actually resolve on your machine. If it causes problems,
remove its definition and simplify the sourcing of `batch_ins.sh` to:

```bash
source batch_ins.sh
```

Then start the model:

```bash
./batch.sh
```

**e)** Check that the model is running correctly:

- If the model stops soon after starting, it probably didn't run well —
  be alarmed if this happens.
- Run `top` to confirm you're running first `batch.exe` and then
  `cmfgen_dev.exe`.
- You should see many soft-links appear in the model directory — a
  dramatic increase in entries when running `ls`.
- Check `batch.log` — it should show the model started running and hasn't
  stopped.
- Open `OUTGEN`. This is a log of what's happening as the model runs. If
  all is going well, the model will enter its first iteration within a
  few minutes, and each subsequent iteration will be reported here.

**f)** If all looks good, let the model run until it reaches convergence
and stops.

The model stopping does not always mean it reached convergence. A few
things to check:

- `OUTGEN` should not report errors associated with ending the
  computation.
- A sign of insufficient convergence is large variations in the mesh
  reported in the last iteration compared to the previous one. The
  largest variations per iteration are reported in `OUTGEN`, and the full
  structure (continuously updated during the run) is in
  `CORRECTION_SUM`. It's good if the largest differences are under a
  percent, and acceptable if they're just a couple of percent.
  `CORRECTION_SUM` usually shows that a specific depth has the largest
  problems, often corresponding to a change in the stellar atmosphere
  such as a recombination front.
- Check that no error is reported in `batch.log` — this is usually where
  segmentation faults show up.
- Another sign of insufficient convergence is a non-smooth profile
  anywhere in the final structure, output in `RVTJ`. Plot it with
  [`dispgen`](analysis-programs/dispgen.md). If a few mesh points behave
  strangely, you probably need more grid points.

**g)** Check that all looks good in the final spectrum by plotting the
spectral energy distribution and normalized spectrum. These are computed
by `batobs.sh`, as you can see inside `batch.sh`. You can also run the
final spectrum computation separately with `./batobs.sh` — see
[Scientific Outputs](scientific-outputs.md).

## Restarting a model

If your model gets interrupted while running (for example, if it's
submitted as a time-limited job on a cluster), you don't need to start
from the beginning. Just run again:

```bash
./batch.sh
```

and your model will continue from the last iteration.

Also delete the files `EDDFACTOR` and `EDDFACTOR_INFO` if present — this
can happen if you want to re-run from an already-converged model.

!!! info "Coming soon"
    Notes on the role of `POINT1` and `POINT2` in restarts.

## Checking the settings of a model

To check whether a model you've already run is plane-parallel or
spherical, look at the `PP_NOV` parameter in `VADAT`: if it's `T`, the
model is plane-parallel. See the workspace notes on plane-parallel vs.
spherical geometry for the full set of related switches.

!!! info "Coming soon"
    A fuller walkthrough of how to inspect the rest of a model's settings.
