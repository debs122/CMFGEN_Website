# Scientific Outputs

## Global parameters

`cmfgen_dev.exe` provides a summary of the computed model in `MOD_SUM`.
Since several parameters change as a function of radius (or optical
depth) in the stellar atmosphere, `MOD_SUM` is useful for identifying the
*effective* values of these parameters — for example the radius,
temperature, and surface gravity.

`MOD_SUM` also contains a summary of several other useful parameters
included in the model. It's worth checking to make sure the properties
you asked for are the properties you got.

## Structure of the stellar atmosphere

`cmfgen_dev.exe` produces the structure of the atmosphere as an output.
Once the model is complete, `RVTJ` contains, for each meshpoint in the
atmosphere, values for the radial location, velocity, temperature,
density, etc.

To extract the ionization structure, plot and save files using
[`dispgen`](analysis-programs/dispgen.md) — it can also be used to
identify where in the stellar atmosphere different spectral features are
formed.

## Spectrum

`cmfgen_dev.exe` does not produce the most accurate spectrum, although it
does output an approximation in `OBSFLUX`. For an accurate spectrum, use
`cmf_flux.exe`.

To do this, prepare a new folder within your model directory (commonly
called `obs`). Inside it, place the `batobs.sh` script and the
`CMF_FLUX_PARAM_INIT` file. Check that the extent of the computed
spectrum does not exceed the extent of the main CMFGEN run:

- `MIN_CF` in `CMF_FLUX_PARAM_INIT` must not be smaller than `MIN_CF` in
  `VADAT`.
- `MAX_CF` in `CMF_FLUX_PARAM_INIT` must not be larger than `MAX_CF` in
  `VADAT`.
- The turbulent velocities (`VTURB_FIX`, `VTURB_MIN`, `VTURB_MAX`) must
  match between `CMF_FLUX_PARAM_INIT` and `VADAT`.

Then start the calculation:

```bash
./batobs.sh
```

Check with `top` that `cmf_flux.exe` is running. When it finishes, clean
up the soft links with `rmlinks` and `clean` — you should then find a
file called `obs_fin`, containing the detailed spectral energy
distribution.

!!! info "Coming soon"
    How to increase spectral resolution.

You can also compute the *continuum* spectral energy distribution the
same way, with a few different settings in `CMF_FLUX_PARAM_INIT` and a
different output naming inside `batobs.sh` (commonly `obs_cont`). The
continuum output has a lower spectral resolution than the detailed
spectrum. To compute the normalized spectrum, `Fnorm = F / Fcont`, you'll
need to interpolate the continuum spectrum onto the detailed spectrum's
wavelength array.

!!! tip
    The continuum run also produces a file called `ewdata_fin`, which can
    contain useful information about the spectral lines included in the
    spectrum — though it usually doesn't include every feature.
