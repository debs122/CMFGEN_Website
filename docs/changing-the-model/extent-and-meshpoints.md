# Extent & Meshpoints

## Changing number of meshpoints

CMFGEN models the atmosphere as a 1-dimensional structure, with depth
points representing different parts of the wind located at different
radii — these are called `ND`. The number of depth points is given in
`MODEL_SPEC`. `ND` plus the number of core rays (`NC`) makes up the
number of impact parameters, `NP = ND + NC`.

You might want to increase the number of depth points if, for example,
`OUTGEN` indicates the grid isn't sufficiently fine.

**a)** Edit `MODEL_SPEC` with the new `ND` — don't forget to also update
`NP`.

**b)** Update `RVSIG_COL` using the `NEW_ND` function of
[`rev_rvsig`](../analysis-programs/rev-rvsig.md). Check that the new
`RVSIG_COL` file contains no `NaN`s.

**c)** Set `LIN_INT` to `true` in `VADAT`.

**d)** You'll need to update the hydrostatic structure: set `DO_HYDRO`
to `true` in `VADAT`, and assign around 5 hydro iterations in
`HYDRO_DEFAULTS`.

**e)** Before restarting the model (if it's a restart), make sure to
erase any file labeled even partially with `EDDFACTOR`.
