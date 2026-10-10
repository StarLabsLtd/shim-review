# Frozen Fedora shim-chain evidence

The Linux lane's retained 10 October 2026 evidence qualifies the reconstructed
enforcing-OVMF chain for source `80596e30435c4d1845bb52319aeb50de2907da0f`,
image `202610061000`, kernel `7.2.8-vale3` and systemd `259.5-2.vale.fc44`.
This packet reconciliation did not rerun tests. Supplied commands/results are
summarized in fedora/runtime-report.txt and fedora/module-runtime.md; original
raw receipts remain in the sibling shim-fedora-20261010 validation directory.

## Exact shim binding

Unsigned shim SHA-256:
`e0fa2d72123cd218ac25c1d46ea991ffe7f1cea7cbe597b87a04dd28cc4df7c7`.
Temporarily signed test shim SHA-256:
`676f9b7f09e4f19c9ff5976c7c767604b95eae3b17eb44d12e7276c7989025fb`.
fedora/shim-body-binding.json proves all original bytes match except PE checksum
bytes 216–219 and security-directory bytes 296–303; a 2032-byte certificate table
was appended at original EOF 1025232. Normalized body SHA-256:
`d6e2ac4ecc282bef63af4a00de1372afb96e4352150fd8bd3adbaa1adae47c2a`.
The producer's signing commands and retained fixture hashes also bind that exact
unsigned input to the tested file. This is not merely a same-certificate inference.

Firmware PK/KEK/db contained only the disposable test authority, certificate
SHA-256 `76e67e1692c20c2cf75774f99472d89d5704982686af28e4187b5e8574318359`.
No Vale/Microsoft certificate or allowed-image hash was enrolled. Vale downstream
trust came from shim. No MOK enrollment or validation-disable operation occurred.
The temporary firmware signature is not a Microsoft signature.

## Observed results

| Case | Result |
| --- | --- |
| Shim → signed systemd-boot → Normal installer UKI | Installer GUI reached |
| Shim → signed systemd-boot → installed current UKI | Encrypted-root unlock, owner graphical login and Polkit authorization passed |
| Shim → signed recovery dispatcher at default loader filename → installer UKI | Installer GUI reached |
| Direct Vale-signed loader without shim | Firmware rejected |
| Unsigned next loader | Shim rejected; absent MokManager did not bypass validation |
| Unsigned UKI / signed UKI with one modified .linux byte | Rejected, with no older-UKI fallback in the fixture |
| Unchanged signed loader / UKI under targeted generation-2 SBAT revocation | Rejected at the expected stage |
| Configured and default next loader missing | No unauthenticated boot; direct Vale fallback rejected |
| Original signed module | Loaded and unloaded |
| Identical unsigned / valid untrusted-signed module payloads | Rejected with signature-specific kernel messages |

Runtime recorded SecureBoot=1, SetupMode=0, integrity lockdown, sig_enforce=Y
and SELinux enforcing. SBAT diagnostics are generic Security Violation:
attribution comes from the controlled policy difference with unchanged signed
inputs, not a claimed literal SBAT error. Module outcomes are process exits and
messages, not syscall errno traces. Existing evidence audits verified all 304
runtime-manifest entries; no mismatch was reported.

## Frozen artifact identities

| Component | SHA-256 |
| --- | --- |
| Original Fedora ISO | a430f999a2fcc93b9d7b12e4386c0cc7572e9cf46695388b7dade74b2763dd5f |
| Installed signed systemd-boot | ba71acffa69d2712a0e5ba6771489f9d4363a08e5a06000f507e1df5b421dda6 |
| Current installed Normal UKI | d0a0cc13655fad321e305afd6578002a9d9d52303a11db67055c0a95eda7698b |
| Signed recovery dispatcher | f7188dbec3bbe42f9413c79f4435949b65fd179381b5f2ac9dd732c191df3879 |
| Normal installer UKI | 2de7ae15c761c245f7453a75f004ed6fadcb5d1731b7c2140ffe16d89ac01677 |
| Dave installer UKI, inventoried rather than commissioned | 435f55cd9d76e25dcf3415b8525320cda4746baee14346f787dfa2005ad27abb |
| Current Dave update UKI, not the retained installed Dave image | f845a72571c04fdfd04e4f7ca3c93a84d55f3e16e0b02891da600fc730007294 |
| Retained installed Normal 0825 UKI | 0b4522498039d27eeb5b428f33b265873ad4bf0aec18bda3553c976147e6b1ed |
| Retained installed Dave 0825 UKI | f55dde8ad2e8b270b62b554bd261961292f62d397cbef781b43f5d93b0832469 |

Complete inventory inspection found NX_COMPAT, valid Vale signatures and
upstream/Fedora/Vale SBAT rows on the inspected EFI surfaces. Installed fallback
BOOTX64 equals the installed systemd loader; the ISO's BOOTX64 is the recovery
dispatcher. No other selectable EFI updater, shim, MokManager or fallback utility
was on the original surfaces. Exact rows are in SBAT.md.

## Limits and preserved history

The original ISO does not ship shim: these are reconstructed fixture chains.
Original ISO/disk/paired firmware and TPM state were preserved. A future
Microsoft-returned binary needs separate exact-artifact qualification, plus
installer/installed ESP and update integration. The frozen vale3 predates later
authenticated-S4 size and worker-thaw fixes; full DAVE/S4 acceptance is not claimed.
Module-key lifecycle remains a separate owner-policy question. These limits do
not require repeating passed cases or waiting on unrelated newer cohorts.

The October 1 Ubuntu validation is retained unchanged in
HISTORICAL-UBUNTU-VALIDATION.md; it is not Fedora evidence. Build-first.log,
build-second.log and reproducibility.txt remain the original shim build evidence.
Publication of the complete public source/raw-evidence set is still pending.
