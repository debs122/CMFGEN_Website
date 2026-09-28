# Changing the Effective Temperature

Changing the effective temperature requires a few more steps than surface
gravity.

**a)** Prepare a new model directory, starting from an already-converged
model.

**b)** The effective temperature is tied to the stellar radius and
bolometric luminosity through the Stefan–Boltzmann law,
L<sub>bol</sub> = 4πR<sub>eff</sub>²σT<sub>eff</sub>⁴. If you change
`TEFF`, you also need to readjust `LSTAR` or `RSTAR` — if you keep one of
these fixed, the other needs to be recalculated. Input the requested
values for `LSTAR`, `RSTAR`, and `TEFF` in `VADAT`.

It's not recommended to change the effective temperature by more than
~10%. For the radius, up to ~70% difference is generally fine, and for
bolometric luminosity, a factor of 3.

*Example: updating `TEFF` from 41,000 K to 42,000 K, keeping the stellar
radius at ~118 × 10¹⁰ cm, and updating the bolometric luminosity from
8.17×10⁵ L<sub>sun</sub> to 8.86×10⁵ L<sub>sun</sub>.*

**c)** Since `TEFF` is a very relevant physical parameter for the
populations, set `LIN_INT` to `false` in `VADAT`.

Also make sure the hydro iterations are switched on (`DO_HYDRO = true`).

**d)** Set the number of hydro-iterations to 5 in `HYDRO_DEFAULTS`.

**e)** For better convergence, it's good to start with lambda-iterations.
In `IN_ITS`, set `DO_LAM_IT` and `DO_LAM_AUTO` to `true`.

**f)** If you chose to update the inner radius rather than the bolometric
luminosity, `RVSIG_COL` needs to be updated to account for the structure
change — see [Radius](radius.md).

**g)** The structure given in `ROSSELAND_LTE_TAB` also needs updating,
using the separate `main_lte.exe` program.

Prepare a folder inside your model folder (commonly called `lte`),
containing the `ltebat.sh` script and a `GRID_PARAM` file. `ltebat.sh`
calls `main_lte.exe`; `GRID_PARAM` provides the range of values within
which the model is expected to lie.

Before running `ltebat.sh`, copy the `VADAT` and `MODEL_SPEC` files from
the main run directory into the `lte` folder. Then run:

```bash
./ltebat.sh
```

Check with `top` that `main_lte.exe` is running — you may also need to
copy `batch_ins.sh` into this folder. `ltebat.sh` produces its own log
(`ltebat.log`) and out-file (`OUTLTE`), useful for tracking progress.

When it finishes, it produces a new `ROSSELAND_LTE_TAB` file — copy it
back to your run directory.

**h)** Now start the model:

```bash
./batch.sh
```
