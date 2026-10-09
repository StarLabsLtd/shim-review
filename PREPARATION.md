# Vale signing preparation — 9 October 2026

Local draft, not submitted or published. The retained unsigned shim embeds
only the public Vale certificate. Do not upload the Vale private key.

## Baseline and evidence

The inspected clean integration checkout is `release/fedora44`, commit
`8a96bc7e75c0c1184ae4aaca20ae8f3298c5f283`. This identifies preparation
inputs, not a frozen or qualified shipping release. Active feature branches
are not silently included.

- Product: Fedora 44 with Pantheon; Normal and optional DAVE installations.
- Kernel input lock: Linux 7.2.8-vale2. The older Ubuntu vale1 module tests in
  KERNEL-EVIDENCE.md do not qualify this kernel or its Fedora package.
- Image tool lock: systemd 259.5-2.vale.fc44. It is not sufficient evidence of
  the final bootloader executable or installed RPM cohort.
- Embedded public certificate SHA-256:
  `1d03918ca00a796621e01d6228ef6fe4ae92f5f4a94a4a59957c9f32cdaa26b0`.
  The packet and current integration certificate are identical.
- Shim SBAT input SHA-256:
  `3f05f580d17b1106052876352c5cd1ccbc303b04f0bce409de967fdcad44addf`.
  The packet and current integration input are identical.
- Shim build logs and reproducibility record were retained from 1 October.
  They are historical build evidence, not new Fedora runtime tests.
- Microsoft Hardware enrollment is approved for seller 96389630. The active
  registered Certum EV certificate is distinct from the embedded Vale key.

## Answers and evidence still required

1. Confirm worldwide deployment, including whether non-Star-Labs firmware
   needs to trust Vale. Explain why distributing a globally trusted shim is
   necessary rather than relying on controlled firmware enrollment.
2. Confirm primary and secondary security-update and emergency-revocation
   responsibilities. Sean's existing public PGP key is included; confirm its
   use for the review challenge and supply Emma's public PGP key.
3. Document production Vale signing-key custody, access controls, backup,
   recovery, rotation and compromise response. Confirm the rationale for
   the leaf certificate's ten-year lifetime and module-signing key lifecycle.
4. Freeze one Fedora shipping cohort: kernel, module certificate, bootloader,
   stub, installer/recovery/installed UKIs and selectable EFI utilities.
   Include executable hashes, signatures, exact SBAT rows, source commits,
   patch queues, config and publicly accessible source references.
5. Test that exact cohort through shim in enforcing firmware: positive
   installer and installed boots; unsigned-loader, modified-UKI and
   unsigned-module rejection with signed positive controls; NX compatibility.
   Include DAVE commissioning/S4 checks where that shipping path differs.
6. Complete README answers, publish the reviewed repository/tag when Sean
   authorizes it, then open the upstream shim-review issue. No contribution
   to other applicants' reviews is claimed without evidence.

## Microsoft handoff after shim-review acceptance

The adjacent `vale-shim-x64-unsigned.cab` is a local staging artifact containing
only `shimx64.efi`, without paths. It has no EV signature and is not upload-ready.
MokManager and fallback are retained for review, not requested for Microsoft
signing; their shipping signatures and integration must be finalized separately.

Once the exact shim is accepted, sign the CAB using the registered organization
EV certificate and its actual token/cloud provider. Verify the CAB signature,
then submit through Partner Center File Signing / Submit New UEFI. Do not
substitute another binary after acceptance. Retain and verify returned outputs,
integrate installer/installed ESP/update paths, and test on firmware trusting
Microsoft UEFI CA 2023. Never ship the temporary QEMU signer as Microsoft-signed.

Requirements checked on 9 October 2026:

- [shim-review template](https://github.com/rhboot/shim-review/blob/main/README.md)
- [Microsoft file-signing requirements](https://learn.microsoft.com/en-us/windows-hardware/drivers/dashboard/file-signing-reqs)
