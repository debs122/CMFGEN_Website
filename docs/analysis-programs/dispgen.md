# dispgen

`dispgen` lets you dig into the structure of a model by plotting profiles
on demand.

!!! warning
    Make sure you've logged into the cluster/remote machine using the
    `-Y` flag in your `ssh` command (`-X` also works) before plotting.

Run `dispgen` from your model's directory — it reads `RVTJ` and asks for a
velocity smoothing factor. Press `ENTER` to skip straight to the option
to ask for structure profiles.

## Ionization structure

To check a specific ion's structure, type `if_X` at the prompt, where `X`
is CMFGEN's nomenclature for that species (e.g. `he` for helium, `carb`
for carbon). After `if_X` → `ENTER` → `/XWINDOW` → `ENTER` or `P`, you'll
get the ion structure plot.

## Opacity structure

!!! info "Coming soon"

## Sobolev optical line depth

1. Go to the model directory and run `./batch.sh ass`, then `dispgen`.
2. Press `ENTER` at the first two prompts, then enter any title when
   asked.
3. At `OPTION [GR]=>`, respond `TAUL_*`, where `*` is the ion species —
   for example `TAUL_CIV`.
4. At `Levels [-1,-1]=>`, press `ENTER`. You'll get a list of common
   transitions for that species — pick the one closest to the transition
   you're looking for and press `ENTER`. You'll see a line of output
   giving the line's position in the model, e.g.:

   ```
   CIV(  6-  4) at 5801.313 Angstroms
   ```

   Copy the suggested wavelength in case your initial input doesn't work.

5. You'll be prompted with `OPTION [GR]=>` again — repeat to plot more
   lines, or press `ENTER` to move on.
6. Pick a graphics type (`/XWINDOW` is typical). At `ANS [P]:`, press
   `ENTER` to plot the line's position.

To zoom in, respond to `ANS [P]:` with `A` — you can then adjust x/y
limits and ticks. Use `P` to replot and `E` to exit. `EDCL` lets you
change the legend labels.

Use `POP_*` to plot level populations and `NETR_*` to plot transition
rates — both take level numbers as input (found in the atomic data) and
work almost identically to the above. `EP_*` plots the line formation
region of a transition, using the same level-data input; it supports
multiple transitions at once.

## Adjust axis range

Type `A` while plotting and follow the prompts to adjust the range and
step size for both axes. Press `ENTER` or `P` again to apply the change.

## Save structure file

Type `WXY` to save your plotted profile — you'll be asked for the desired
wavelength range, which curves to write, and a filename. The output file
contains pairs of columns per curve: radius (decreasing order) and the
corresponding structural quantity, e.g. an ion abundance.
