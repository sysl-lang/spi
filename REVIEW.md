# Review findings

From the spi/sd review of 2026-10-28 (against spi dev 7852bfa). Findings about
`sd` are recorded in that repository.

| # | Finding | Status | Commit |
|---|---|---|---|
| 1 | `select(false)` left `active` true after releasing the bus when the CS pin failed | Fixed: `active` tracks bus ownership and is cleared whenever the bus is released; pin-failure, select-failure and acquire-failure (`Busy`) tests | c5750f2, 281d1fc |
| 7 | `select(bool)` open/close pair, no transaction shape | Fixed: `select()`, `deselect()` and a `transaction(op)` bracket that deselects whatever `op` returned | 281d1fc, f483f59 |
| 8 | `Exclusive` reconfigures per deselected byte; the name misdescribes it | Fixed: renamed `CsDevice`; forced deselect documented; `Bus.configure` is documented as cheap when the controller already holds the configuration, and a test shows a caching controller reprogrammed only when the device or its configuration changes | 281d1fc, review-fixes |
| 9 | Shared bus has no test or example | Fixed: two devices over one pointer handle, arbitration and per-device configuration restore tested; README describes the handle pattern | 281d1fc, review-fixes |
| 10 | README: no code example; inverted buffer-lifetime sentence; moving pin | Fixed: tested usage example, "an implementation must not keep a caller's buffer", install snippet pins the released version | 281d1fc, review-fixes |
| 12 | Positional constructors | Fixed: spi has no `Settings` or large fake; the multi-field test constructors use named arguments | review-fixes |
| 13 | Mode-indexed tests | n.a.: spi has none; every case is its own named test | — |
| 14 | Too much public | n.a. for spi: helpers (`finish`, `begin`, `deselected`) are private, `CsDevice` fields are `private[spi]` | — |
| 17 | Idiom nits | Fixed: `Mode.M0`..`M3`; portable probe's extern is `probe_byte`, no longer shadowing `exchange`; no unused loop variables in spi | 281d1fc |
