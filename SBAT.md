# Shim SBAT inventory

Extracted on 9 October 2026 with `objcopy --dump-section .sbat=/dev/stdout`.
Each retained unsigned binary (`shimx64.efi`, `mmx64.efi`, `fbx64.efi`)
contains these identical rows:

```csv
sbat,1,SBAT Version,sbat,1,https://github.com/rhboot/shim/blob/main/SBAT.md
shim,4,UEFI shim,shim,1,https://github.com/rhboot/shim
shim.vale,1,Star Labs,vale-shim,16.1,https://starlabs.systems
```

This inventory does not cover the Fedora bootloader, systemd-stub, UKIs or
firmware-update binaries. Their final executable listings remain required.
