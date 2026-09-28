# Changing Abundances

Changing composition is one of the main things you'll want to do when
working with stripped stars. For spectral classification, you also need
to make sure the necessary elements and their relevant ionization stages
are included.

Input abundances live in `VADAT`. Some are listed as negative numbers:
this indicates the number after the minus sign is a **mass fraction**.
Positive numbers are **number fractions relative to a reference
element**.

It's recommended to use mass fractions for elements heavier than oxygen.
For the lighter, usually more abundant elements (typically H, He, C, N,
O), the most abundant element is normally chosen as the reference, with
its own relative number abundance set to 1.0. Relative number abundances
follow:

```
[CARB] = n_C / n_He = (X_C / 12) / (X_He / 4)
```

where `n_C` is the number fraction of carbon, `n_He` the number fraction
of helium, `X_C` the mass fraction of carbon, and `X_He` the mass
fraction of helium — here helium is the reference element.

## Changing the composition of existing elements

**a)** Calculate the new composition following the convention above. As
long as the changes stay within the recommended factors (roughly a
factor of 3 for hydrogen/helium, 5 for other elements — see
[Changing the Model](index.md)), this should work. Fill it out in `VADAT`
in a new model directory created with `cpmod`.

*Example: increasing the helium abundance at the expense of hydrogen,
setting `X_He` to 0.5:*

```
Before:                      After:
1.0        [HYD/X]           1.0                [HYD/X]
0.16       [HE/X]            0.2558853634       [HE/X]
3.9720E-05 [CARB/X]          4.90105766E-05     [CARB/X]
6.2400E-04 [NIT/X]           7.698493932E-04    [NIT/X]
1.3520E-04 [OXY/X]           1.668372569E-04    [OXY/X]
```

Since mass fractions add up to 1, it's worth writing a small helper
program that takes mass fractions and a reference element and outputs
the number-fraction ratios.

**b)** Since you're editing elemental abundances, you can't reuse the
previous populations — set `LIN_INT` to `false` in `VADAT`.

**c)** Make sure `DO_LAM_IT`, `DO_LAM_AUTO`, and `DO_T_AUTO` are set to
`true` in `IN_ITS`.

**d)** It's a good idea (though not strictly necessary) to also switch on
the hydro iterations and recompute `ROSSELAND_LTE_TAB`: set `DO_HYDRO`
to `true` in `VADAT`, set the number of hydro iterations to 5 in
`HYDRO_DEFAULTS`, and run `main_lte.exe` via `ltebat.sh` (see
[Effective Temperature](effective-temperature.md)), copying the
resulting `ROSSELAND_LTE_TAB` back to the main directory. This can help
convergence.

**e)** Start the model:

```bash
./batch.sh
```

## Adding or removing elements / ions

Adding additional ions can be a difficult change to make. Here's a
sequence of steps to add extra ions/ionization stages:

1. `cpmod` from a converged model. Extra files are needed that `cpmod`
   doesn't copy by default: `RVTJ`, `EDDFACTOR`, `EDDFACTOR_INFO` — copy
   these separately from the converged model.
2. To include extra ionization stages, edit `MODEL_SPEC`. You'll see
   entries like:

   ```
   30,30,30     [HI_ISF]
   69,69,69     [HeI_ISF]
   ```

   Generalizing, each line is `NV,NS,NF   [XzV_ISF]` — the levels
   included for a given ion/ionization stage:

   - `NF` = number of full atomic levels used (`NF ≤` levels in the data
     file)
   - `NS` = number of super-levels — bundled levels, for speed
     (`NS ≤ NF`)
   - `NV` = intermediate count; in practice set `NV = NS`

   This must always hold: `NV ≤ NS ≤ NF`.

   `XzV` stands for any ion name like `HI`, `HeI`, ... . Note that
   whenever there's a singly ionized ion, CMFGEN treats it as `2`, not
   `II` — e.g. `He2` not `HeII`, `C2` not `CII`. Other stages follow
   Roman numerals as usual (`CIII`, `CIV`, `NIII`, `NIV`, etc). To decide
   your `NV`, `NS`, `NF` values, see
   [Accounting for ionization stages and super-levels](#accounting-for-ionization-stages-and-super-levels)
   below.

3. Edit `batch.sh` (or `batch_ins.sh`) to point at the relevant files for
   the new ion — look at neighboring blocks for the pattern:

   | Variable          | Points to      |
   | ------------------ | -------------- |
   | `XzV_F_OSCDAT`      | `osc_data`      |
   | `XzV_F_TO_S`        | `f_to_s_<N>`    |
   | `XzV_COL_DATA`      | `col_data`      |
   | `PHOTXzV_A`         | `phot_data_A`   |
   | `PHOTXzV_B`         | `phot_data_B` (only if present) |
   | `XzV_AUTO_DATA`     | `auto_data` (only if present)   |

4. Add the new ion's abundance in `VADAT`.
5. In `VADAT`, set `F,F [DIE_XzV]` for each new ion, unless a die-data
   file exists for it — in which case set `T` on either flag so CMFGEN
   reads `DIEXzV`. A missing file with the flag set to `T` is a hard
   stop.
6. Set `T [AUTO_ADD]` in `VADAT`.
7. Initialize with fixed J (strongly recommended — manual case 5):

   ```
   VADAT:   [USE_FIXED_J]=T   [FIX_T]=T
   IN_ITS:  [DO_LAM_IT]=T
            [DO_LAM_AUTO]=T
            [DO_T_AUTO]=T
            [DO_GT_AUTO]=T
   ```

8. Double-check you've copied `RVTJ`, `EDDFACTOR`, `EDDFACTOR_INFO` from
   the earlier converged run.
9. Switch off the hydro iterations for this run — run a separate model
   afterwards with hydro on, keeping this one simple.
10. Submit:

    ```bash
    ./batch.sh
    ```

## Accounting for ionization stages and super-levels

How to decide `NV`, `NS`, `NF`:

1. Go to the `$atomic` directory — this is the atomic data. Inside, you'll
   see folders per species (`H`, `HE`, `NIT`, ...), and inside each,
   folders per ionization stage (`I`, `II`, ...).
2. Say you want to add NV to your model: go into
   `$atomic/NIT/VI/`. You'll see dated subdirectories — different model
   atoms, updated at different times. Prefer the latest one unless
   there's a reason not to.
3. Look at the file called `f_to_s`. There may be multiple variants, e.g.
   `f_to_s_64`, `f_to_s_98` — the numbers are the superlevel counts.
   Higher superlevel counts are more computationally expensive; use your
   judgement.
4. `f_to_s` contains all the relevant atomic data — read it carefully.
5. The number of full atomic levels is in the header. This gives you
   `NF` (or, if you don't want to use the full set, cut the atomic model
   down using the number in the right-hand column and use that as `NF`).
6. Once you've decided which `NF` to use, find the number of superlevels
   corresponding to that `NF` value in the file — this gives `NS`.
7. Set `NV = NS`.
