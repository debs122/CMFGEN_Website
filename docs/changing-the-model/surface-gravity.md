# Changing Surface Gravity

Changing the surface gravity is one of the easier changes to make in a
CMFGEN model.

**a)** Copy a starting model to a new directory using `cpmod` and
`out2in`, as described in [Running a Model](../running-a-model.md).

**b)** In `VADAT`, edit:

- `LOGG` to the new value you'd like (e.g. from 3.6 to 3.0).
- Set `LIN_INT` to `true`.
- Set `DO_HYDRO` to `true` to switch on the hydro iterations.

**c)** In `HYDRO_DEFAULTS`, update the number of hydro-iterations —
usually 5 (`N_ITS`) is sufficient. As CMFGEN runs, `ITS_DONE` increases
while `N_ITS` counts down.
