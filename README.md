# spi

Freestanding Sysl interfaces for eight-bit SPI buses and chip-selected devices.
No OS, allocator or MCU dependency. ISC licensed. MCU packages and firmware
implement `Bus` and `ChipSelect`; part drivers use `Device`.

```
dependencies {
  spi { git = "github.com/sysl-lang/spi", version = "0.2.0" }
}
```

## Use

```sysl
import sh.sysl.spi.{Bus,ChipSelect,Config,Device,Error,device,config,Mode}

struct Controller
end Controller
impl Bus for Controller
    configure(*self, cfg: Config) -> Result[unit,Error] = Ok(())
    exchange(*self, byte: u8) -> Result[u8,Error] = Ok(byte)

struct Pin
end Pin
impl ChipSelect for Pin
    set_active(*self, active: bool) -> Result[unit,Error] = Ok(())

read_id() -> Result[u8,Error]
    var flash = device(Controller(),Pin(),config(1000000,Mode.M3))
    flash.transaction(d ->
        d.write([0x9fu8])?
        d.exchange(0xff))
```

`transaction(op)` selects the device, runs `op`, and deselects it whatever `op`
returned; the first error wins. `select()` and `deselect()` are there for spans
that do not fit one closure. A part driver that holds a `Device` needs nothing
else.

## Devices and shared buses

`CsDevice` (built by `device(bus, cs, config)`) keeps each device's clock, mode
and bit order and holds bus arbitration from `select()` to `deselect()`. A
deselected `exchange` or bulk call arbitrates on its own, which SD cards need
for their clocks with CS high. Selecting a selected device does nothing; there
is no nesting count. Deselecting an inactive device still takes the bus and
drives CS inactive.

Two devices share a controller by sharing a handle to it: the `Bus` is usually
a small struct holding a pointer to the controller, copied into each device, and
it overrides the bounded `acquire`/`release` with the platform's arbitration.
The defaults suit one exclusively owned controller. `configure` is called at
every acquisition, since another device may have changed the controller; a
controller already holding the same configuration should make it cheap. The
package supplies no mutex or global locking policy.

Bulk methods can be overridden with DMA. A transfer finishes before its call
returns, so an implementation must not keep a caller's buffer. Settings specify
a clock ceiling; unsupported modes and orders must be rejected. Timing and part
protocols belong to their driver packages.

Development branch: `dev`. Run `./scripts/gate.sh` for tests and five
instantiated freestanding builds. Hardware-controller implementations are
supplied by the application; compile probes do not execute on target hardware.
