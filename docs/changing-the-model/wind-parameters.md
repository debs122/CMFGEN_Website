# Changing Wind Parameters

There are several wind parameters CMFGEN accounts for:

- Mass-loss rate (`MDOT`)
- Terminal wind speed (`VINF`)
- Exponent for the wind velocity law (`BETA`)
- Wind velocity law equation (`VEL_LAW`, `VEL_OPT`)
- Clumping factor (`DO_CL`, `CL_LAW`, `CL_PAR_1`, `CL_PAR_2`, `N_CL_PAR`)

Changing any of `MDOT`, `VINF`, or `BETA` requires updating
`RVSIG_COL` — see
[Changing the wind structure](../analysis-programs/rev-rvsig.md#changing-the-wind-structure).
