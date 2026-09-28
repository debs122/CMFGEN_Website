# Changing the Radius

## Changing the innermost radius

**a)** Update `RSTAR` in `VADAT` after preparing a new model directory
with `cpmod`. Keep in mind `RSTAR` is given in units of 10¹⁰ cm.

*Example: updating `RSTAR` from 118.1177 × 10¹⁰ cm (~17 R<sub>sun</sub>)
to 20 R<sub>sun</sub> = 139.14 × 10¹⁰ cm. Since `LSTAR` is held strict in
`VADAT` and doesn't change as the code runs, it needs updating too — here
to 1.018331×10⁶ L<sub>sun</sub>. Mass doesn't need updating; CMFGEN
handles that on its own.*

**b)** Set `LIN_INT` to `false` in `VADAT`.

**c)** Update `IN_ITS` to switch on the lambda iterations for stability:
set `DO_LAM_IT` and `DO_LAM_AUTO` to `true`.

**d)** If the temperature is kept fixed, there's no need to run hydro
iterations (`DO_HYDRO` can stay `false`). If you do switch them on, the
effective temperature and surface gravity given as input to `VADAT` take
priority, and `RSTAR` may change so that the bolometric luminosity
(`LSTAR`) and effective values are reached at the reference optical depth
(`TAU_REF`, see below).

## Changing the optical depth at which inputs are given (`TAU_REF`)

**Background.** Opacity (κ<sub>ν</sub>) describes how easily a photon is
absorbed or scattered by the medium, as a function of wavelength. It
depends on both the wavelength and the local conditions (temperature,
density, abundances). Optical depth (τ<sub>ν</sub>) is related to opacity
as an inverted cumulative sum:

```
τ_ν(r) = ∫ κ_ν(r') dr'   (integrated from r to infinity)
```

So optical depth increases as you move further into a stellar
atmosphere — but it's still wavelength-dependent, so the same optical
depth isn't reached at the same radius for all wavelengths. For example,
optical depth 1 could occur at 10 R<sub>sun</sub> for one wavelength but
20 R<sub>sun</sub> for another, if there's an ionization edge in between.

The **photosphere** is the optical depth at which about half the photons
escape and half are absorbed or scattered — this occurs at τ = ⅔.

Since optical depth varies with wavelength, the photosphere shifts
depending on wavelength too, which complicates referring to something
like "the radius of a star." For this reason, an average,
wavelength-independent optical depth is used instead — the **Rosseland
mean opacity**, ⟨τ<sub>Rosseland</sub>⟩. Surface properties of a star are
usually quoted at ⟨τ<sub>Ross</sub>⟩ = ⅔.

If a property changes throughout the atmosphere, its value at the
photosphere is called *effective* — e.g. effective temperature, but also
effective surface gravity or effective radius.

CMFGEN lets you choose at what optical depth your input parameters
(`VADAT` values) are located. If the atmosphere is mostly transparent,
the exact optical depth value matters less; in a denser atmosphere, you
may see a big difference in, say, radius or temperature between optical
depth ⅔ and 1 or 10.

!!! note
    If your input values come from a stellar evolution code (e.g. MESA),
    keep in mind that code doesn't account for the stellar wind, so its
    outputs may sit well inside the actual atmosphere — this effect can
    be significant, especially for Wolf-Rayet-type winds. See
    [Sander, Hamann & Todt (2014)](http://adsabs.harvard.edu/abs/2014A%26A...564A..30G).

`TAU_REF`, in `HYDRO_DEFAULTS`, controls where the input optical depth is
set.

**a)** Prepare a model using `cpmod` and `out2in`.

**b)** Update (or add) `TAU_REF` in `HYDRO_DEFAULTS`.

*Example: moving the input parameters inward, to `TAU_REF = 20`.*

**c)** Adapt the outer part of the grid. By default, CMFGEN assumes the
input values are at `TAU_REF = ⅔`. To allow a different value, also set:

- `OB_OPT = SPECIFY` — allows the grid to differ from the default (the
  manual recommends this option).
- `NOB_PARS` — the number of additional grid points to add (e.g. 4).
- `OB_P1`, `OB_P2`, `OB_P3`, … — the factor by which Δτ should be divided
  in the default grid, to refine the outer wind. Example:
  `OB_P1 = 31`, `OB_P2 = 10.33`, `OB_P3 = 4.429`, `OB_P4 = 2.067`.

**d)** Update the hydrostatic structure: switch on `DO_HYDRO` in `VADAT`,
set `LIN_INT` to `false`, and in `HYDRO_DEFAULTS` set `IN_ITS` to 5 and
`ITS_DONE` to 0.

**e)** Switch on the lambda-iterations in `IN_ITS` (`DO_LAM_IT = true`).

**f)** Set the `HYDRO_OPT` option — see [Hydro Options](#hydro-options)
below.

**g)** Update `RVSIG_COL` using [`rev_rvsig`](../analysis-programs/rev-rvsig.md).

**h)** Update `ROSSELAND_LTE_TAB` (see
[Effective Temperature](effective-temperature.md)).

**i)** Run the model.

## Changing the maximum radius

The maximum radius describes how large the extent of the atmosphere is.

**a)** Update `MAX_R` in `HYDRO_DEFAULTS` in a newly copied model. Also
update `N_ITS` to 5 (and `ITS_DONE` to 0, if not already).

**b)** In `VADAT`, switch on the hydro iterations (`DO_HYDRO = true`) and
set `LIN_INT` to `false`. You don't need to update `RMAX` in `VADAT`
directly — it updates automatically.

**c)** It's generally a good idea to switch on the lambda-iterations here
too (`DO_LAM_IT` in `IN_ITS`).

**d)** Run the model — `RVSIG_COL` does not need manual updating in this
case, it happens automatically.

## Hydro Options

These options are used when updating the hydrostatic structure of the
star, via `HYDRO_OPT` in `HYDRO_DEFAULTS`:

`DEFAULT`
:   The standard option.

`FIXED_R`
:   Keeps the radius at a pre-specified `TAU_REF` fixed. To preserve the
    specified effective temperature, the luminosity is updated instead.

`FIXED_V_FLUX`
:   Useful for O stars — attempts to preserve the V-band flux.

`FIXED_LUM`
:   Keeps the luminosity fixed at the expense of temperature. Useful for
    WR models, where luminosity is the key variable controlling the
    observed spectrum. Once the code finds two adjacent grid points
    where the optical depth brackets the reference value τ<sub>ref</sub>,
    it linearly interpolates between them to get a more accurate
    reference radius, then recalculates the effective temperature from
    it — so the resulting temperature differs slightly from the input,
    typically by less than 1%.
