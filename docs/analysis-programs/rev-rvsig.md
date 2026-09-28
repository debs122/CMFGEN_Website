# rev_rvsig

`rev_rvsig` helps you update the starting structure of a model. Run it
with:

```bash
rev_rvsig
```

!!! tip
    If the alias isn't working, use the full path:
    `$cmfdist/exe/rev_rvsig.exe`

Not every change requires updating `RVSIG_COL`:

| Parameter                        | Change `RVSIG_COL`? |
| --------------------------------- | -------------------- |
| `MDOT` (mass-loss rate)           | Yes                   |
| `VINF` (terminal velocity)        | Yes                   |
| `BETA` (velocity law)             | Yes                   |
| `ND` (number of depth points)     | Yes                   |
| `RSTAR` (core radius)             | Yes                   |
| Abundances                        | No                    |
| `log g`                           | No                    |
| Model atoms / species             | No                    |

## Changing the wind structure

When changing wind parameters (`MDOT`, `VINF`, `BETA`, `CL_PAR_1`,
`CL_PAR_2`) or other parameters that affect the wind structure (e.g.
`TEFF`), you need to update `RVSIG_COL` before running the model. Opening
the existing file shows it outlines these structure parameters for each
meshpoint.

**a)** Move the current `RVSIG_COL` file aside:

```bash
mv RVSIG_COL RVSIG_COL_OLD
```

**b)** Start `rev_rvsig`.

**c)** Select `RVSIG_COL_OLD` as the starting file (press `ENTER` to
accept the default suggestion).

**d)** If `RVSIG_COL_OLD` contains density and clumping factors, press
`T`; otherwise `F`.

**e)** At the next step, choose `MDOT`.

**f)** Input first the old mass-loss rate, then the new one. To keep it
the same, enter the same value twice.

**g)** Choose the wind velocity law — option 2 (the standard beta-law) is
typical.

**h)** Fill in the new terminal wind speed — to keep it unchanged, use the
same `VINF` value from `VADAT`.

**i)** Fill in `BETA` (check `VADAT` if you want to keep it unchanged).

**j)** For the connection velocity — the transition between photosphere
and wind — the standard value of 10 km/s (close to the sound speed) is
usually a safe default.

**k)** Name the new file `RVSIG_COL`.

**l)** Optionally plot the updated wind velocity profile via `/xwindow`
then `P`, or skip plotting with `/null` and exit with `E` then `E`.

## Changing the number of meshpoints

An easy way to change the number of depth points (`ND`) is `rev_rvsig`'s
`NEW_ND` function.

**a)** Start `rev_rvsig` (or `$cmfdist/exe/rev_rvsig.exe` if the alias
isn't recognized).

**b)** Feed in the name of the previous `RVSIG_COL` file (commonly renamed
to `RVSIG_COL_OLD` first).

**c)** Answer whether the old file contains clumping factors or density.

**d)** Choose `NEW_ND`. You may be asked about a rounding error — if the
difference looks small, answer `true`.

**e)** Input the new number of depth points.

**f)** Name the new file `RVSIG_COL` so CMFGEN recognizes it.

**g)** Optionally plot with `/xwindow`, or exit directly with `/null`,
`E`, `E`.
