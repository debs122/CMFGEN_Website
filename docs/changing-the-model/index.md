# Changing the Model

[Running a Model](../running-a-model.md) covers rerunning a model exactly
as pre-computed. This section is about changing parameters to compute a
new, different model — starting from an existing one via `cpmod`.

## How much can you change at once?

As a rule of thumb, don't push a single change too far from the starting
model. Recommended maximum change factors from a starting model to a new
one:

| Parameter                | Max. change factor |
| ------------------------- | ------------------- |
| `TEFF`                    | 1.1                  |
| `LSTAR`                   | 3.0                  |
| H, He                     | 3.0                  |
| C, N, O, Si, Fe, …         | 5.0                  |
| `MDOT`                    | 3.0                  |
| `VINF`                    | 3.0                  |
| `RSTAR`                   | 1.7                  |

Pick a topic:

- [Surface Gravity](surface-gravity.md)
- [Effective Temperature](effective-temperature.md)
- [Abundances](abundances.md)
- [Radius](radius.md)
- [Extent & Meshpoints](extent-and-meshpoints.md)
- [Wind Parameters](wind-parameters.md)
- [Useful Commands](useful-commands.md)
