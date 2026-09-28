# Troubleshooting

## `ADJUST_CORRECTIONS`

Sometimes you're not happy with the final result of your model — for
example, the model finishes before all iterations complete (`NUM_ITS` in
`IN_ITS`), but the final maximum difference between the last and
second-to-last iteration is still uncomfortably large. Creating an
`ADJUST_CORRECTIONS` file can help.

**a)** Look at `CORRECTION_SUM` — where's the problem? Identify the
depth-point numbers that bracket the problematic region.

**b)** Create a new file called `ADJUST_CORRECTIONS` in the main run
directory:

```
0.3   [RELAX]
XX    [LST]
XX    [LEND]
```

Fill in the meshpoint numbers that bound the problematic region — the
smallest number goes in `LST`, the largest in `LEND`.

**c)** Remove `RVTJ`, `MOD_SUM`, and `OUTGEN` from the main run directory
(removing `batch.log` too isn't a bad idea).

**d)** If the model has already reached the number of iterations set in
`IN_ITS` (`NUM_ITS`), increase it by another 200.

**e)** Restart the model:

```bash
./batch.sh
```

## Possible error with hydrostatic structure — large error in photosphere

When the surface gravity is decreased substantially (roughly below
`log g < 4`), CMFGEN can appear to converge nicely, while `OUTGEN`
reports an error that looks approximately like:

```
******************************************************************************
Possible error with hydrostatic structure --- large error in photosphere
              Mean error is -1.30E+00
Root mean squared error is  1.87E+00
       Maximum error is  3.76E+00
    Stars mass (Msun) is  5.70E+00
      R(phot, 10^10cm) is  7.69E+01
           log G(phot) is  3.11E+00
       Specified log g is  3.15E+00
******************************************************************************
```

**a)** If you see this in `OUTGEN`, verify that the parameters you
specified in `VADAT` are actually reached in `MOD_SUM`.

**b)** If they aren't reached:

!!! info "Coming soon"
    A worked solution for this case hasn't been written up yet.

## Possible error messages

**`error in changing tau_ref`**

```
Updating hydrostatic structure of the model
Error -- for the DEFAULT HYDRO_OPTION, TAU_REF must be 2/3
```

This happens when you try to change `TAU_REF` without also setting a
non-default `HYDRO_OPT`. CMFGEN assumes `HYDRO_OPT = DEFAULT` unless
specified otherwise in `HYDRO_DEFAULTS`. In recent CMFGEN versions, you
need to specify a `HYDRO_OPT` option if you want a `TAU_REF` other than
⅔ — see [Hydro Options](changing-the-model/radius.md#hydro-options).

**`GET_KEY_STRING`**

```
Error in GET_KEY_STRING
Unable to locate string containing the key CHK_NG
```

This means `VADAT` doesn't contain `CHK_NG`. This can happen when
starting from a model that was run with an older CMFGEN version — check
the release notes and update your files accordingly.

## `LIN_INT` quick reference

| Situation                                  | `LIN_INT` | `IT_ON_T`         |
| -------------------------------------------- | --------- | ------------------ |
| New model, new stellar parameters            | `F`        | `T`                  |
| Same stellar params, different atoms/ND/options | `T`        | `F`                  |
| `MDOT` or `VINF` changed                     | `F`        | `T`                  |
| Only abundances changed                      | `T` or `F`  | Depends on setup     |
