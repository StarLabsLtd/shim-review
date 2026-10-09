# Vale shim boot-chain validation

On 1 October 2026, unmodified shim 16.1 was built twice with the permanent Vale
public certificate. All three EFI binaries reproduced byte-for-byte. The source
archive's SHA-256 matches upstream; its detached signature verifies against
Peter Jones's published signing subkey
`02093E0D19DDE0F7DFFBB53C1FD3F540256A1372`.

Shim SHA-256:
`e0fa2d72123cd218ac25c1d46ea991ffe7f1cea7cbe597b87a04dd28cc4df7c7`.
Build recipe: the packet-root `Dockerfile`. The build embeds `shim.vale,1` SBAT and sets
NX compatibility, with upstream's PE-layout checks enabled. Build logs, package versions, unsigned binaries and public certificate are
in this packet.

## Runtime checks

Stock enforcing OVMF trusted only a temporary QEMU firmware signer, with no
Microsoft or Vale certificate in firmware `db`. This signer represents the
authority that will sign shim; it is not a Microsoft signature. Only shim was
signed with it. Systemd-boot, MokManager and the unchanged H7 UKIs were signed
with the permanent Vale key.

| Check | Result |
| --- | --- |
| Firmware → shim → systemd-boot → installer UKI | Native installer menu reached |
| Live and installed kernel enforcement | `7.2.8-vale1`, Secure Boot enabled, integrity lockdown |
| Fresh Normal installation, then explicit shim boot entry | First-user setup and logged-in Pantheon reached without installer media; `/usr` read-only EROFS |
| Direct Vale-signed bootloader, bypassing shim | Firmware rejected: Access Denied |
| Shim followed by unsigned systemd-boot | Shim rejected: Security Violation |
| Signed UKI with one byte altered in `.linux` | Rejected before Linux: Security violation |

Evidence is retained locally under repository-relative
`out/shim-chain-validation/` and `.cache/vale-shim-*/`; it is not included
in this packet or published.
The test installation was `.cache/vale-shim-positive/target.qcow2`; its explicit
EFI entry is `\EFI\vale\shimx64.efi`, with systemd-boot beside it. The installer
fixture changed only H7's ESP, outside its authenticated data prefix. Original
H7 still has SHA-256
`c2507c423aab550c80c9da7c9592c6736b3c8981fb6306fd0e5119531162fc73`.
The temporary VM disk and modified installer copy were retired after recording
results to conserve space. The USB and staged release are unchanged. These are Normal boot-chain checks;
they do not repeat DAVE commissioning or sealed-S4 validation through shim.

## Before submission and shipping

- Rebuild the final release with the reviewed additive Vale SBAT changes and
  include its exact boot stack in the review request. Current H7 remains unchanged;
  the component fixtures below are not the final release.
- Integrate the eventual Microsoft-signed shim into installer, installed ESP and
  update handling; do not ship the temporary QEMU-signed copy.
- Confirm company registration, primary and secondary security contacts and
  their public PGP keys, EV certificate and Microsoft Partner Center access.
- Confirm signing-key custody and security-update/revocation responsibilities.
- Publish the frozen review repository/tag and submit a shim-review issue, then
  submit the accepted binary to Microsoft. Nothing has been submitted yet.
- Test the returned Microsoft-signed binary and fresh DAVE commissioning on
  hardware. Since 27 June 2026, new signatures require firmware to trust the
  Microsoft UEFI CA 2023.

Requirements: [shim-review](https://github.com/rhboot/shim-review/blob/main/README.md)
and [Microsoft file signing](https://learn.microsoft.com/en-us/windows-hardware/drivers/dashboard/file-signing-reqs).

## Additive SBAT follow-up

The `259.5-0ubuntu3.4+vale2` package adds two Vale SBAT rows without replacing
Ubuntu or upstream rows. Native ukify adds `uki.vale`, retaining generic `uki`.
The fixture selected the explicit installer stub. Emitted bootloader/stub
and signed installer rows were inspected.
The rebuilt chain reached the H7 installer in enforcing stock OVMF with Secure
Boot enabled and integrity lockdown. Multiprofile UKI fixture signatures verify
under the existing separate runtime/initrd authorities. These are component
fixtures using H7 content, not a new release ISO or USB update.

The earlier PCR11 and recovery-metadata export fixtures are retained locally
in `out/vale-sbat-validation/` (repository-relative, not included in this
packet or published). They do not establish acceptance of the current
firmware-provisioned SRK/systemd-tpm2 LUKS2 design or the final shipping stack.
