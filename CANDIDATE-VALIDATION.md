# Build and validation

## Binary and certificate

Unsigned shim SHA256:
`6ee83a224ff0a1490a54795e668f0ac669098944eaef6abc4fc5fdd581c2e275`.

Embedded public DER SHA256:
`d247d96c7dec493c3b89749e02852f032c247c9da80407fbdccaf2db51381b61`.

The certificate is a self-signed RSA-4096/SHA256 code-signing leaf, valid from
29 October 2026 22:31:38 UTC to 29 October 2031 22:31:38 UTC.
See KEY-PROTECTION.md for key custody and recovery.

Two builds in separate fresh containers produced identical shim, MokManager
and fallback binaries. Both compiled from the retained pristine shim source
and pinned toolchain layer:
`b4c0fb6268022cc86eecec13484768640598f8483962da1e4a66a678efdcd6c9`.
The package lists match the original build. The Dockerfiles provide the full
pinned recipe; build logs and hashes are included.

The replacement changes the embedded certificate. Shim source, flags, default
loader and SBAT inputs are unchanged. The new DER occurs once in shim; the
previous DERs are absent. MokManager and fallback remain unsigned build
companions and are not shipped.

## Signed components

The tested chain uses systemd-boot, the recovery dispatcher and the Normal
061000 UKI. Their input, unsigned and signed hashes are recorded in
candidate-signature-evidence.json.

Each original matched its recorded hash. We removed its old outer signature
in a copy, signed it through the HSM and verified the new signature against
the RSA-4096 certificate. All payload sections, section headers, SBAT, NX and
DLL characteristics were unchanged. The normalized PE bodies also match;
only the checksum, security-directory fields and outer certificate tables
change.

## OVMF tests

The tests use fresh enforcing OVMF with a GPT ESP on virtio. Firmware trusts
only a disposable fixture authority used to sign the exact submitted shim
body. The Vale key is trusted through shim. No validation-disable setting or
user MOK enrollment is used. EFI files are read back from the test ESP and
checked against their hashes before boot.

| Test | Result |
| --- | --- |
| Shim → signed systemd-boot → signed Normal UKI | Signature and SBAT verification succeeded; UKI sections and embedded Linux loaded |
| Shim → signed recovery dispatcher → signed Normal UKI | Signature and SBAT verification succeeded; UKI sections and embedded Linux loaded |
| Recovery route with unsigned Normal UKI | Refused: no signatures found / Security Policy Violation |
| Old-key, unsigned or tampered loader | Each refused with Security Violation |
| Old-key, unsigned or tampered UKI | Each refused with image-loading Security violation |
| Valid signed loader or UKI under its targeted generation-2 SBAT revocation | Each refused at the expected stage |
| Vale-signed loader launched directly without shim | Firmware Access Denied |
| Missing next loader | Not Found; no bootable option |

normal-chain-evidence.json and normal-chain-excerpts.txt bind the positive
verification traces and unsigned recovery control. candidate-firmware-evidence.json
records the other controls and earlier incomplete runs. SBAT attribution comes
from changing only the revocation policy while keeping the signed input fixed.

## Test scope

These tests establish authentication and loading, not a completed OS or
installer boot. Recovery uses the same Normal UKI at the dispatcher's expected
Normal.efi path to test that route. It does not test installer functionality.
The temporary fixture signature is not Microsoft's.

Normal represents the shared signature-verification path. DAVE-specific
behaviour is outside these tests. Earlier incomplete runs and
historical OS-boot results remain recorded separately in
candidate-firmware-evidence.json and VALIDATION.md.

The existing 50-file source manifest and two source archives are unchanged.
Kernel and module results remain tied to their original kernel and configuration.
Microsoft's returned shim will need its own signature and boot checks before
deployment.
