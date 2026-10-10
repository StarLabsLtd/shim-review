# Vale shim signing application — local draft

Not submitted. This draft now records the frozen Fedora 44 test cohort
80596e30435c4d1845bb52319aeb50de2907da0f / image 202610061000 / vale3.
Contact verification, production key lifecycle and public source references
remain open; see PREPARATION.md. Historical Ubuntu evidence is retained separately.

### What organization or people are asking to have this signed?

Star Labs Online Limited (Star Labs) — https://starlabs.systems

### What's the legal data that proves the organization's genuineness?

Star Labs Online Limited, England and Wales, company 10602146.
[Companies House register](https://find-and-update.company-information.service.gov.uk/company/10602146),
[Star Labs contact details](https://starlabs.systems/pages/contact-us).
Partner Center Hardware enrollment is approved for seller 96389630.
The active Certum EV code-signing certificate is issued to Star Labs Online
Limited (company 10602146), by Certum Extended Validation Code Signing 2021 CA.
Its SHA-1 thumbprint is 0623917D5780EC28A1E21582CA5C5C5CFA71E2C2.

### What product or service is this for?

Vale, the Star Labs immutable Linux desktop, based on Fedora 44 and elementary
Pantheon. Normal installation and an optional DAVE profile.

### What's the justification that this really does need to be signed for the whole world to be able to boot it?

Vale is intended for both Star Labs and non-Star-Labs PCs. It ships its own
signed kernel and systemd-boot. Firmware on third-party PCs does not normally
trust the Vale certificate, so a Microsoft-signed shim is needed to boot with
Secure Boot enabled without requiring users to enroll a new firmware key.

### Why are you unable to reuse shim from another distro that is already signed?

Vale signs its bootloader and UKIs with the Vale certificate. Another distribution’s shim does not embed that certificate.

### Who is the primary contact for security updates, etc.?

Sean Rhodes, Star Labs, sean@starlabs.systems. Public PGP fingerprint:
40BCEEFFAC908E3B5F37399D57A9369D52843D8C (sean-rhodes.asc).
Sean owns security updates. Use of this key for review correspondence and
emergency-revocation responsibilities remain to be confirmed.

### Who is the secondary contact for security updates, etc.?

Emma Marsden, Star Labs, emma@starlabs.systems, confirmed secondary contact.
Public PGP fingerprint: 0954B68651694FEAE5703B9B9F1033E526D797F4
(emma-marsden.asc), generated 10 October 2026 with a two-year primary-key
expiry. Her encrypted key bundle was emailed on 10 October 2026; successful
import/decryption and the reviewer's email/key ownership challenge remain
unconfirmed. Backup responsibilities also remain to be confirmed.

### Were these binaries created from the 16.1 shim release tar?

Yes. Source SHA-256: 46319cd228d8f2c06c744241c0f342412329a7c630436fce7f82cf6936b1d603. Detached signature verification is recorded in source-signature.txt; the signature and Peter Jones’s public key are included.

### URL for a repo that contains the exact code which was built to result in your binary:

Pending public frozen repository/tag. Exact public build inputs are included locally; nothing has been published.

### What patches are being applied and why:

No shim source patches. The Dockerfile supplies the Vale certificate and SBAT, enables code-signing EKU support, sets systemd-bootx64.efi as the next loader, and enables NX plus PE-layout checks.

### Do you have the NX bit set in your shim? If so, is your entire boot stack NX-compatible and what testing have you done to ensure such compatibility?

Yes. Shim and all inventoried Fedora EFI components have NX_COMPAT.
Enforcing OVMF booted the installer, installed owner session and recovery
dispatcher path. Unsigned loaders, unsigned/tampered UKIs and direct loader
bypass were rejected. The test-signed shim retains the exact submitted body;
the signer is a disposable test authority, not Microsoft. See VALIDATION.md.

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

Linux 7.2.8-vale3, Fedora RPM kernel-vale-7.2.8-3.vale.fc44.x86_64.
Exact config, patch queue, source binding and runtime evidence are recorded
in KERNEL-EVIDENCE.md. This is the frozen test cohort, not later S4 candidates.

### Is upstream commit [1957a85b0032a81e6482ca4aab883643b8dae06e "efi: Restrict efivar_ssdt_load when the kernel is locked down"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1957a85b0032a81e6482ca4aab883643b8dae06e) applied?

Linux 7.2.8 contains the corresponding lockdown checks. Source inspection confirms EFI SSDT/configfs table restrictions and debugger-write restrictions; public source evidence is pending.

### Is upstream commit [75b0cea7bf307f362057cc778efe89af4c615354 "ACPI: configfs: Disallow loading ACPI tables when locked down"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=75b0cea7bf307f362057cc778efe89af4c615354) applied?

Linux 7.2.8 contains the corresponding lockdown checks. Source inspection confirms EFI SSDT/configfs table restrictions and debugger-write restrictions; public source evidence is pending.

### Is upstream commit [eadb2f47a3ced5c64b23b90fd2a3463f63726066 "lockdown: also lock down previous kgdb use"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=eadb2f47a3ced5c64b23b90fd2a3463f63726066) applied?

Linux 7.2.8 contains the corresponding lockdown checks. Source inspection confirms EFI SSDT/configfs table restrictions and debugger-write restrictions; public source evidence is pending.

### How does your signed kernel enforce lockdown when your system runs with Secure Boot enabled?

CONFIG_SECURITY_LOCKDOWN_LSM_EARLY=y and CONFIG_LOCK_DOWN_KERNEL_FORCE_INTEGRITY=y.
The frozen Fedora guest recorded SecureBoot=1, SetupMode=0, integrity lockdown,
module sig_enforce=Y and SELinux enforcing. See KERNEL-EVIDENCE.md.

### Do you build your signed kernel with additional local patches? What do they do?

The frozen vale3 queue contains 24 patches for CFR/ACPI runtime transport,
encrypted hibernation, hardware support and DAVE sleep policy. The included
fedora/kernel-series and fedora/vale-kernel-patches.tar.gz identify the exact
queue; all patch bytes match source 80596e3 in the supplied audit. The later
authenticated-S4 size and worker-thaw fixes are not in this cohort. Public
complete-source references remain to be finalized.

### Do you use an ephemeral key for signing kernel modules?

CONFIG_MODULE_SIG_ALL uses the autogenerated certs/signing_key.pem build key
with SHA-512 signatures. Build logs record key generation and ordinary removal
of its build tree; that does not establish secure erasure, absence of other
copies or a production lifecycle policy. The recovered vale3 public certificate
has SHA-256 169b9ad28e02eefa597b11f276c68bd2c8ea97024cb7cea52284c95023e8973d.
The exact guest accepted the signed control and rejected identical unsigned
and valid untrusted-signed controls. See KERNEL-EVIDENCE.md; a production
ephemerality/cross-build trust policy remains unresolved.

### If not, please describe how you ensure that one kernel build does not load modules built for another kernel.

CONFIG_MODULE_SIG_FORCE is unset, but the recorded guest had sig_enforce=Y
and integrity lockdown. Unsigned and untrusted-signed same-payload controls
were rejected with signature-specific kernel messages; the signed control
loaded and unloaded. These are process exit statuses/messages, not traced
syscall errno values. This does not prove rejection of another build's module
if that build reused a trusted key and compatible ABI; the key lifecycle policy
must resolve that case.

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

The Vale private Secure Boot key is outside the repository and build contexts;
only its public DER is embedded. The planned production arrangement is two
encrypted removable copies kept in locked offsite storage, with one brought
to the office and connected to the build machine only for signing sessions.
KEY-PROTECTION.md defines the proposed custody, session, recovery and compromise
requirements. These controls are planned, not yet attested as operational;
implementation and recovery checks remain required before production use.

### Do you use EV certificates as embedded certificates in the shim?

No. The embedded Vale certificate is separate. An EV certificate for signing
Microsoft submission CABs is registered and active in Partner Center. It is not
the Vale Secure Boot certificate embedded in shim.

### Are you embedding a CA certificate in your shim?

No. It is a dedicated code-signing leaf (CA:FALSE). The proposed policy requires
annual review and incident-triggered replacement regardless of the certificate's
ten-year validity. The final validity rationale and rotation mechanism remain
to be confirmed.

### Do you add a vendor-specific SBAT entry to the SBAT section in each binary that supports SBAT metadata ( GRUB2, fwupd, fwupdate, systemd-boot, systemd-stub, shim + all child shim binaries )?

Yes for the inventoried frozen cohort: shim.vale, systemd-boot.vale,
systemd-stub.vale, uki.vale and vale-recovery-dispatch rows are present.
Upstream/Fedora rows are retained. Exact rows and component identities are
in SBAT.md. No other selectable EFI updater was found on these surfaces.

### Please provide the exact SBAT entries for all binaries you are booting directly through shim.

See SBAT.md for the shim, systemd-boot, recovery dispatcher and UKI listings.
MokManager/fallback are retained unsigned review companions, not shipped tools
on the inventoried ISO or installed ESP.

### If shim is loading GRUB2 bootloader, which modules are built into your signed GRUB2 image?

Not applicable: systemd-boot, not GRUB2.

### If you are using systemd-boot on arm64 or riscv, is the fix for [unverified Devicetree Blob loading](https://github.com/systemd/systemd/security/advisories/GHSA-6m6p-rjcq-334c) included?

Not applicable: x86-64 only.

### What is the origin and full version number of your bootloader (GRUB2 or systemd-boot or other)?

Fedora systemd 259.5-2.vale.fc44, with Vale's additive SBAT changes.
The installed signed loader SHA-256 is
ba71acffa69d2712a0e5ba6771489f9d4363a08e5a06000f507e1df5b421dda6.
Frozen source/tool records and component bindings are in fedora/source-freeze.json
and VALIDATION.md; public complete-source publication remains open.

### If your shim launches any other components apart from your bootloader, please provide further details on what is launched.

Default next loader: systemd-bootx64.efi. One tested installer path places the
Vale-signed recovery dispatcher at that filename; it launches the signed
Normal installer UKI. Other tested paths use signed systemd-boot. The stock
frozen ISO/ESP do not yet contain shim, MokManager or fallback; shipping
integration remains open. Missing-loader tests ended without unauthenticated boot.

### If your GRUB2 or systemd-boot launches any other binaries that are not the Linux kernel in SecureBoot mode, please provide further details on what is launched and how it enforces Secureboot lockdown.

The frozen installed systemd-boot selection contains signed UKIs; the installer
recovery dispatcher also selects signed UKIs. The complete inspected ISO/ESP
lists no other EFI updater or utility. Retained older Normal/Dave UKIs are
inventoried separately from the current-only runtime fixture; their presence
does not claim full DAVE acceptance. See VALIDATION.md and SBAT.md.

### How do the launched components prevent execution of unauthenticated code?

Firmware verifies shim; shim verifies Vale-signed systemd-boot; systemd-boot verifies the UKI before execution. Kernel integrity lockdown and module-signature verification protect kernel execution.

### Does your shim load any loaders that support loading unsigned kernels (e.g. certain GRUB2 configurations)?

The exact frozen Fedora chain rejected unsigned and tampered UKIs. Targeted
loader/UKI SBAT revocations also rejected unchanged signed images. See
VALIDATION.md; the reconstructed fixture is not yet shipping ESP integration.

### What kernel are you using? Which patches and configuration does it include to enforce Secure Boot?

Linux 7.2.8-vale3 / kernel-vale-7.2.8-3.vale.fc44.x86_64, built from Linux 7.2.8
plus the included frozen 24-patch queue and Fedora configuration. The exact
packaged config is fedora/kernel-config. Integrity lockdown is forced and
modules are signed; runtime identity and controls are in KERNEL-EVIDENCE.md.
Public complete-source references remain to be finalized.

### What contributions have you made to help us review the applications of other applicants?

Pending Sean’s confirmation. No contribution is claimed.

### Add any additional information you think we may need to validate this shim signing application.

Local draft only. Hardware enrollment is approved; no review issue or Microsoft
submission has been made. Frozen Fedora fixture-chain validation passed;
public source/tag, contact verification, key lifecycle and shipping shim
integration remain open. The ISO still has no shim. Returned Microsoft-signed
binary qualification follows signing. Full DAVE/S4 release testing is separate
from the stated shim-chain evidence and is not claimed here.

Historical Ubuntu package identities and results are retained in
HISTORICAL-UBUNTU-KERNEL.md and HISTORICAL-UBUNTU-VALIDATION.md. They do not
replace the selected frozen Fedora evidence above.
