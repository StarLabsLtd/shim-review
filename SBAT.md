# Shim SBAT inventory

Extracted on 9 October 2026 with `objcopy --dump-section .sbat=/dev/stdout`.
Each retained unsigned binary (`shimx64.efi`, `mmx64.efi`, `fbx64.efi`)
contains these identical rows:

```csv
sbat,1,SBAT Version,sbat,1,https://github.com/rhboot/shim/blob/main/SBAT.md
shim,4,UEFI shim,shim,1,https://github.com/rhboot/shim
shim.vale,1,Star Labs,vale-shim,16.1,https://starlabs.systems
```

## Frozen Fedora 202610061000 cohort

The installed signed systemd-boot (SHA-256
ba71acffa69d2712a0e5ba6771489f9d4363a08e5a06000f507e1df5b421dda6)
retains these rows, also present in its identical installed BOOTX64 fallback:

```csv
sbat,1,SBAT Version,sbat,1,https://github.com/rhboot/shim/blob/main/SBAT.md
systemd-boot,1,The systemd Developers,systemd,259,https://systemd.io/
systemd-boot.fedora,1,Fedora Linux,systemd,259.5-2.vale.fc44,https://bugzilla.redhat.com/
systemd-boot.vale,1,Star Labs,systemd,259.5-2.vale.fc44,https://starlabs.systems
```

The ISO recovery dispatcher (SHA-256
f7188dbec3bbe42f9413c79f4435949b65fd179381b5f2ac9dd732c191df3879),
also exercised as shim's next loader in the recovery-path fixture:

```csv
sbat,1,SBAT Version,sbat,1,https://github.com/rhboot/shim/blob/main/SBAT.md
vale-recovery-dispatch,1,Star Labs,vale-recovery-dispatch,1,https://starlabs.systems
```

The inventoried Normal/Dave installer UKIs, installed current/retained UKIs
and public update UKIs have these rows. Their distinct hashes and surfaces are
listed in VALIDATION.md; inventory of a Dave image is not commissioned-DAVE
runtime acceptance:

```csv
sbat,1,SBAT Version,sbat,1,https://github.com/rhboot/shim/blob/main/SBAT.md
systemd-stub,1,The systemd Developers,systemd,259,https://systemd.io/
systemd-stub.fedora,1,Fedora Linux,systemd,259.5-2.vale.fc44,https://bugzilla.redhat.com/
systemd-stub.vale,1,Star Labs,systemd,259.5-2.vale.fc44,https://starlabs.systems
uki,1,UKI,uki,1,https://uapi-group.org/specifications/specs/unified_kernel_image/
uki.vale,1,Star Labs,vale,1,https://starlabs.systems
```

The unsigned systemd linux stub RPM input has the first four UKI rows above;
ukify adds uki/uki.vale to emitted UKIs. No other selectable EFI utility or
updater was found on the inspected ISO/ESP. In particular, the original surfaces
do not contain shim or its companions. The retained runtime tests used targeted
systemd-boot.vale,2 and uki.vale,2 revocation policies and refused unchanged
signed inputs. Copied evidence and its limits are described in VALIDATION.md.
