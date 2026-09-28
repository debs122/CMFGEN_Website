# CMFGEN

<div class="cmfgen-hero" markdown>

# CMFGEN

<p class="cmfgen-tagline">
Probing the Universe through Spectroscopy
</p>

</div>

CMFGEN, written by D. John Hillier, solves the equations of statistical
equilibrium and radiative transfer in spherical or plane-parallel geometry
to model the atmospheres of hot stars, stripped-envelope stars, and
supernovae. This site collects a practical, hands-on tutorial for
installing CMFGEN, running and modifying models, and interpreting its
output.

!!! note "Work in progress"
    This documentation mirrors an internal tutorial that is still being
    filled in. Sections marked **Coming soon** have not been written up
    yet — check back, or fill them in yourself if you have the details.

<div class="grid cards" markdown>

- :material-download-box:{ .lg .middle } **Installation**

    ---

    Dependencies, Makefile configuration, compilation, and environment
    setup for a workstation or a cluster.

    [:octicons-arrow-right-24: Get started](installation.md)

- :material-play-circle:{ .lg .middle } **Running a Model**

    ---

    Copy an existing model, check its settings, start a run, and confirm
    it actually reached convergence.

    [:octicons-arrow-right-24: Running a model](running-a-model.md)

- :material-chart-line:{ .lg .middle } **Scientific Outputs**

    ---

    Global parameters, atmosphere structure, and computing an accurate
    synthetic spectrum with `cmf_flux`.

    [:octicons-arrow-right-24: Scientific outputs](scientific-outputs.md)

- :material-magnify-scan:{ .lg .middle } **Analysis Programs**

    ---

    `dispgen`, `rev_rvsig`, and `plt_spec` — the tools for inspecting and
    editing a model's structure.

    [:octicons-arrow-right-24: Analysis programs](analysis-programs/dispgen.md)

- :material-tune-variant:{ .lg .middle } **Customizing Models**

    ---

    Change surface gravity, effective temperature, abundances, radius,
    mesh resolution, or wind parameters.

    [:octicons-arrow-right-24: Changing the model](changing-the-model/index.md)

- :material-wrench-cog:{ .lg .middle } **Troubleshooting**

    ---

    Diagnose slow or failed convergence, and work through
    `ADJUST_CORRECTIONS` and common error messages.

    [:octicons-arrow-right-24: Troubleshooting](troubleshooting.md)

</div>
