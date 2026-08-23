# toolchain-riscv (WCH GCC 15.2.0, Windows x86_64)

WCH's patched RISC-V GCC 15.2.0 (`riscv32-wch-elf-`), taken from MounRiver
Studio 2, packaged for PlatformIO.

Supports C++20 modules and C++23, and accepts WCH's
`__attribute__((interrupt("WCH-Interrupt-fast")))` as well as the `xw` and
bitmanip extensions.

## Known limitation: C++ exceptions do not work

`libstdc++.a` and `libsupc++.a` in this toolchain are built **without unwind
tables** — `eh_throw.o` has no `.eh_frame` section in any multilib. There is
therefore no FDE for `__cxa_throw`, phase 1 of unwinding ends immediately, and
every `throw` reaches `std::terminate`.

Verified on a CH32H417QEU6: the firmware prints up to the throw and stops. It
is not affected by `--specs=nano.specs` either way, and it links cleanly, so
the failure appears only at run time.

If you need working exceptions, use a stock `riscv-none-elf` GCC (xpack's
build has the unwind tables) or clang.

## Provenance

MounRiver Studio 2, `components/WCH/Toolchain/RISC-V Embedded GCC15`,
reported version `g5115c7e44-dirty` 15.2.0.

Host `.exe`/`.dll` files are stripped with `--strip-unneeded`; target
libraries are partially `--strip-debug`'d. `share/info` and `share/man` are
removed. Compiling and linking C and C++ were verified after stripping.
