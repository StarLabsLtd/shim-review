# Vale shim signing application — local draft

Not submitted. Contact, EV certificate and Microsoft account, key custody and final boot-stack fields remain open.

The tested kernel below is vale1. The next candidate uses vale2; its TPM tests
do not replace the signed/unsigned-module and complete boot-chain checks.

### What organization or people are asking to have this signed?

Star Labs Online Limited (Star Labs) — https://starlabs.systems

### What's the legal data that proves the organization's genuineness?

Star Labs Online Limited, England and Wales, company 10602146.
[Companies House register](https://find-and-update.company-information.service.gov.uk/company/10602146),
[Star Labs contact details](https://starlabs.systems/pages/contact-us).
EV certificate issuer/subject remain pending.

### What product or service is this for?

Vale, the Star Labs immutable Linux desktop, based on Ubuntu 26.04 and elementary Pantheon. Normal installation and an optional DAVE profile.

### What's the justification that this really does need to be signed for the whole world to be able to boot it?

Vale ships its own signed kernel and systemd-boot. Existing Star Labs laptops
without the Vale certificate need a trusted shim to boot without enrolling a
new key. Worldwide deployment justification beyond firmware we control remains
to be confirmed before submission.

### Why are you unable to reuse shim from another distro that is already signed?

Vale signs its bootloader and UKIs with the Vale certificate. Another distribution’s shim does not embed that certificate.

### Who is the primary contact for security updates, etc.?

Sean Rhodes, Star Labs, sean@starlabs.systems. Public PGP key and responsibilities pending confirmation.

### Who is the secondary contact for security updates, etc.?

Emma Marsden, Star Labs, emma@starlabs.systems. Public PGP key and responsibilities pending confirmation.

### Were these binaries created from the 16.1 shim release tar?

Yes. Source SHA-256: 46319cd228d8f2c06c744241c0f342412329a7c630436fce7f82cf6936b1d603. Detached signature verification is recorded in source-signature.txt; the signature and Peter Jones’s public key are included.

### URL for a repo that contains the exact code which was built to result in your binary:

Pending public frozen repository/tag. Exact public build inputs are included locally; nothing has been published.

### What patches are being applied and why:

No shim source patches. The Dockerfile supplies the Vale certificate and SBAT, enables code-signing EKU support, sets systemd-bootx64.efi as the next loader, and enables NX plus PE-layout checks.

### Do you have the NX bit set in your shim? If so, is your entire boot stack NX-compatible and what testing have you done to ensure such compatibility?

Yes. NX compatibility is set in shim and the tested downstream stack. Enforcing OVMF reached the installer and installed Normal Pantheon session. Unsigned next loaders, direct bootloader bypass and modified UKIs were rejected. See VALIDATION.md. Final rebuilt SBAT-stack acceptance is pending.

### What exact implementation of Secure Boot in GRUB2 do you have? (Either Upstream GRUB2 shim_lock verifier or Downstream RHEL/Fedora/Debian/Canonical-like implementation)

Not applicable: systemd-boot, not GRUB2.

### Do you have fixes for all the following GRUB2 CVEs applied?

Not applicable: systemd-boot, not GRUB2.

### If shim is loading GRUB2 bootloader, and if these fixes have been applied, is the upstream global SBAT generation in your GRUB2 binary set to 5?

Not applicable: systemd-boot, not GRUB2.

### Were old shims hashes provided to Microsoft for verification and to be added to future DBX updates?

First Vale signing application; no previously Microsoft-signed Vale shim has shipped.

### Does your new chain of trust disallow booting old GRUB2 builds affected by the CVEs?

The supported chain uses systemd-boot, not GRUB2.

### If your boot chain of trust includes a Linux kernel:

Linux 7.2.8-vale1; source/config evidence is being prepared for publication.

### Is upstream commit [1957a85b0032a81e6482ca4aab883643b8dae06e "efi: Restrict efivar_ssdt_load when the kernel is locked down"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1957a85b0032a81e6482ca4aab883643b8dae06e) applied?

Linux 7.2.8 contains the corresponding lockdown checks. Source inspection confirms EFI SSDT/configfs table restrictions and debugger-write restrictions; public source evidence is pending.

### Is upstream commit [75b0cea7bf307f362057cc778efe89af4c615354 "ACPI: configfs: Disallow loading ACPI tables when locked down"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=75b0cea7bf307f362057cc778efe89af4c615354) applied?

Linux 7.2.8 contains the corresponding lockdown checks. Source inspection confirms EFI SSDT/configfs table restrictions and debugger-write restrictions; public source evidence is pending.

### Is upstream commit [eadb2f47a3ced5c64b23b90fd2a3463f63726066 "lockdown: also lock down previous kgdb use"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=eadb2f47a3ced5c64b23b90fd2a3463f63726066) applied?

Linux 7.2.8 contains the corresponding lockdown checks. Source inspection confirms EFI SSDT/configfs table restrictions and debugger-write restrictions; public source evidence is pending.

### How does your signed kernel enforce lockdown when your system runs with Secure Boot enabled?

CONFIG_SECURITY_LOCKDOWN_LSM_EARLY=y and CONFIG_LOCK_DOWN_KERNEL_FORCE_INTEGRITY=y. Secure Boot and integrity lockdown were observed in live and installed Normal QEMU boots.

### Do you build your signed kernel with additional local patches? What do they do?

The vale1 inputs inspected for this packet carry CFR and its ACPI runtime transport, encrypted hibernation, DRRS timing matching, Bluetooth runtime power, card-reader idle/voltage fixes and StarFighter HDA identifiers. Exact public source/config references are pending.

### Do you use an ephemeral key for signing kernel modules?

CONFIG_MODULE_SIG_ALL uses the autogenerated certs/signing_key.pem build key with SHA-512 signatures. Module-key ephemerality is unconfirmed: autogeneration does not prove destruction or prevent reuse. The packaged kernel's public certificate has SHA-256 cff069aa0c23acc80cc4d0f796904415163c07152b360c263f7a1726c0ebf819. The exact packaged kernel rejected an unsigned module and loaded the unchanged signed control in enforcing OVMF/shim. This used a diagnostic UKI and temporary firmware-test CA; final stack acceptance and key custody remain open. See KERNEL-EVIDENCE.md.

### If not, please describe how you ensure that one kernel build does not load modules built for another kernel.

See the module-signing answer above. CONFIG_MODULE_SIG_FORCE is unset; forced integrity lockdown rejects unsigned or untrusted modules. The isolated runtime check confirmed unsigned rejection with EKEYREJECTED and successful loading of the unchanged signed control. Lockdown is forced independently of Secure Boot; the test does not attribute enforcement solely to firmware state.

### If you use vendor_db functionality of providing multiple certificates and/or hashes please briefly describe your certificate setup.

One Vale code-signing certificate, with no allow-listed binary hashes. Public DER: vale-secure-boot.der.

### If there are allow-listed hashes please provide exact binaries for which hashes are created via file sharing service, available in public with anonymous access for verification.

None.

### If you are re-using the CA certificate from your last shim binary, you will need to add the hashes of the previous GRUB2 binaries exposed to the CVEs mentioned earlier to vendor_dbx in shim. Please describe your strategy.

First application; no previous Vale shim/GRUB2 release to revoke.

### Is the Dockerfile in your repository the recipe for reproducing the building of your shim binary?

Yes: docker build . uses the submission-root Dockerfile. The base image, package snapshot and release archive are pinned. Two independent local builds reproduced all three EFI binaries byte-for-byte; see reproducibility.txt.

### Which files in this repo are the logs for your build?

build-first.log, build-second.log and build-packages.txt. Source verification: source-signature.txt. Runtime checks: VALIDATION.md.

### What changes were made in the distro's secure boot chain since your SHIM was last signed?

First application.

### What is the SHA256 hash of your final shim binary?

e0fa2d72123cd218ac25c1d46ea991ffe7f1cea7cbe597b87a04dd28cc4df7c7

### How do you manage and protect the keys used in your shim?

The Vale private Secure Boot key is outside the repository and build contexts; only its public DER is embedded. Production custody, recovery, rotation and revocation responsibilities remain to be confirmed.

### Do you use EV certificates as embedded certificates in the shim?

No. The embedded Vale certificate is separate. An EV certificate for signing
Microsoft submission CABs has not yet been obtained.

### Are you embedding a CA certificate in your shim?

No. It is a dedicated code-signing leaf (CA:FALSE). The ten-year validity and rotation rationale still require confirmation.

### Do you add a vendor-specific SBAT entry to the SBAT section in each binary that supports SBAT metadata ( GRUB2, fwupd, fwupdate, systemd-boot, systemd-stub, shim + all child shim binaries )?

shim.vale is present in all three shim binaries. Rebuilt systemd-boot/stub retain their generic and Ubuntu rows and add distinct Vale rows. UKIs also add uki.vale. Exact final stack listings and native acceptance are pending.

### Please provide the exact SBAT entries for all binaries you are booting directly through shim.

shim.vale is present in all three shim binaries. Rebuilt systemd-boot/stub retain their generic and Ubuntu rows and add distinct Vale rows. UKIs also add uki.vale. Exact final stack listings and native acceptance are pending.

### If shim is loading GRUB2 bootloader, which modules are built into your signed GRUB2 image?

Not applicable: systemd-boot, not GRUB2.

### If you are using systemd-boot on arm64 or riscv, is the fix for [unverified Devicetree Blob loading](https://github.com/systemd/systemd/security/advisories/GHSA-6m6p-rjcq-334c) included?

Not applicable: x86-64 only.

### What is the origin and full version number of your bootloader (GRUB2 or systemd-boot or other)?

Ubuntu systemd 259.5-0ubuntu3.4 with Vale sleep-backend and additive SBAT patches: 259.5-0ubuntu3.4+vale2.

### If your shim launches any other components apart from your bootloader, please provide further details on what is launched.

Default next loader: systemd-bootx64.efi. Upstream MokManager and fallback companions are built; final shipping ESP integration is pending. No custom unsigned loader is used.

### If your GRUB2 or systemd-boot launches any other binaries that are not the Linux kernel in SecureBoot mode, please provide further details on what is launched and how it enforces Secureboot lockdown.

Supported Vale entries launch signed UKIs. Any selectable EFI utilities, including firmware updaters, must be inventoried and validated before submission.

### How do the launched components prevent execution of unauthenticated code?

Firmware verifies shim; shim verifies Vale-signed systemd-boot; systemd-boot verifies the UKI before execution. Kernel integrity lockdown and module-signature verification protect kernel execution.

### Does your shim load any loaders that support loading unsigned kernels (e.g. certain GRUB2 configurations)?

The tested supported chain rejects unsigned or modified UKIs. See VALIDATION.md.

### What kernel are you using? Which patches and configuration does it include to enforce Secure Boot?

The tested package is Linux 7.2.8-vale1. Its inspected inputs were kernel/series
and the Ubuntu-derived configuration plus kernel/config/vale.fragment. These
paths now contain newer work and are not frozen source references. Integrity
lockdown is forced; modules are signed. Public frozen source/config is pending.

### What contributions have you made to help us review the applications of other applicants?

Pending Sean’s confirmation. No contribution is claimed.

### Add any additional information you think we may need to validate this shim signing application.

Local draft only. Microsoft signing, final boot-stack acceptance, public
source/tag, contacts, key custody and hardware testing remain pending. DAVE
will use standard systemd-tpm2 LUKS2 tokens beneath a firmware-provisioned SRK;
that firmware contract and complete OS integration are not yet accepted.

## Next kernel (not the module-tested kernel above)

The next candidate package is
`linux-image-7.2.8-vale2_7.2.8-vale2_amd64.deb`, SHA-256
`c43ea4ff76886eab58fe2d71174845b0cca5e947f03bf79a250e6c325c4e3bf4`.
The module-tested vale1 package is SHA-256
`7d65f67b7ff6689ad92c1a88f3e52e7f8bbdc846293e8214e7f9e45893487101`.
Vale2 has reviewed native TPM NoRetry/malformed-response QEMU evidence, but
its signed/unsigned-module controls and final signed installed boot are pending.
The final submission must identify one actual shipping stack, its SBAT rows,
public source/config/patches and results; this draft does not select that stack.
