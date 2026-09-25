# mingw-w64-toolchain for Arch Linux ARM (Aarch64 Host)

![mingw-w64-toolchain banner][05]

Aim: I have a ArchLinux ARM64 machine. I want to be able to compile/build and
debug native Windows binaries (both for i686 and x64) inside my Linux machine.
It might sound stupid, but I do not care! Repo is ready and it is working!

## Toolchain Build and Install Order

Installation order:

1. mingw-w64-binutils
2. mingw-w64-headers
3. mingw-w64-headers-bootstrap -> dummy pthread headers, needed until winpthreads exists

Build and install above three packages. Before building the real gcc, we
need a stage-1 compiler (C only, no libgcc) to build the runtime.

1. mingw-w64-gcc-base -> needs binutils + headers
2. mingw-w64-crt -> needs gcc-base + headers-bootstrap
3. mingw-w64-winpthreads -> needs gcc-base + crt

Before installing mingw-w64-winpthreads you should uninstall
mingw-w64-headers-bootstrap (they conflict, winpthreads brings the real
pthread headers).

1. mingw-w64-gcc -> needs crt + winpthreads, remove gcc-base before installing

That is the toolchain (same five packages as the Arch x86_64
`mingw-w64-toolchain` group). gcc-base and headers-bootstrap are gone after
this step.

The whole chain, in the right order, with the removals done for you:

```bash
scripts/build-toolchain.sh                  # toolchain
scripts/build-toolchain.sh --set part2      # helpers + zlib, gmp, openssl 1.1/1.0
scripts/build-toolchain.sh --set gdb        # Windows gdb.exe + gdbserver.exe
scripts/build-toolchain.sh --set apps       # tier-1 libraries, tools, raylib
scripts/build-toolchain.sh --set apps2      # curl chain, SDL, FLTK, ImGui, boost
```

### Important Packages

The cross-build wrappers first, then the libraries that need them.

1. mingw-w64-environment
2. mingw-w64-pkg-config
3. mingw-w64-configure
4. mingw-w64-cmake
5. mingw-w64-zlib
6. mingw-w64-gmp
7. mingw-w64-openssl-1.1 -> needs zlib
8. mingw-w64-openssl-1.0 -> needs zlib

### Windows gdb

Aim: I want to be able to debug native windows binaries inside my linux arm
machine. This gdb build provides`gdbserver.exe`. With this we can remote
debug.

**IMPORTANT**: Even I install the mingw-w64-gdb package, I still can use
my stock GDB. Because GDB v16.2 supports multi-architecture! `mingw-w64-gdb`
package did not built with python support but the stock gdb did! I can use
gef of pwndbg with stock gdb!

gdb.exe (with TUI) and a static gdbserver.exe for remote debugging from the
Linux host gdb.

1. mingw-w64-expat
2. mingw-w64-mpfr -> needs gmp
3. mingw-w64-libgnurx
4. mingw-w64-libiconv
5. mingw-w64-ncurses
6. mingw-w64-gdb -> needs all of the above

## Resources

* [ArchLinux Packages][01]
* [ArchLinux AUR Packages][02]
* [ArchLinux ARM PKGBUILDS][03]
* [Fedora Package Sources][04]

## Author

BlueDeviL // SCT

## Last Words

> The night keeps its breath  
> Wasting hours with the debug lines  
> Dark shell answers back
>
> Blue DeviL // SCT  
> 24/09/2026

## License

AGPLv3

[01]: https://www.archlinux.org/packages/
[02]: https://aur.archlinux.org/
[03]: https://github.com/archlinuxarm/PKGBUILDs
[04]: https://src.fedoraproject.org/
[05]: https://github.com/user-attachments/assets/a61ef1d0-5869-4325-a789-4c2a336edfeb
