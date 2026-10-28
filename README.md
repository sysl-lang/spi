# spi

Freestanding Sysl interfaces for eight-bit SPI buses and chip-selected devices.
No OS, allocator or MCU dependency. ISC licensed. MCU packages and firmware
implement `Bus` and `ChipSelect`; part drivers use `Device`.

`Exclusive` retains each device's clock/mode/order and holds bus arbitration
through the entire CS assertion. Bus handles for shared hardware override bounded
`acquire`/`release`; defaults suit one exclusively owned controller. The adapter
also arbitrates deselected clocks, needed by SD cards, and cleans up after errors.
It supplies no mutex or global locking policy. Caller code must always end a
selected transaction, including after exchange/read/write errors.

Bulk methods can be overridden with DMA. Transfers finish before callbacks return;
settings specify a clock ceiling. Unsupported modes/orders must be rejected.
Timing and part protocols belong to their driver packages.

Release v0.1.0 is added with:

```
dependencies {
  spi { git = "github.com/sysl-lang/spi", version = "0.1.0" }
}
```

Development branch: `dev`. Run `./scripts/gate.sh` for tests and five
instantiated freestanding builds. Hardware-controller implementations are supplied
by the application; compile probes do not execute on target hardware.
