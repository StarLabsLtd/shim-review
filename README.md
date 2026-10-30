# Vale shim signing application — Star Labs Vale x86_64

Candidate: [starlabs-shim-x86_64-20261030](https://github.com/StarLabsLtd/shim-review/tree/starlabs-shim-x86_64-20261030).
[Application #591](https://github.com/rhboot/shim-review/issues/591); both contacts are verified.

The shim uses a five-year RSA-4096 certificate generated in a YubiHSM 2 FIPS 140-3.
Two builds produced identical binaries. The tested chain uses systemd-boot,
the recovery dispatcher and a Normal UKI.

Build and test results are in CANDIDATE-VALIDATION.md. Boot-chain sources are in
BOOT-CHAIN-SOURCES.md and SOURCE-MANIFEST.json. Key custody is described in
KEY-PROTECTION.md; submission status is in PREPARATION.md. Historical Fedora
and Ubuntu results are labelled separately.

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
Pantheon. Normal is used for the boot-chain tests. The optional DAVE profile
uses the same retained kernel, initrd and stub with a different command line.

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
Sean owns security updates and coordinates review correspondence and any
required revocation. His [contact-challenge response](https://github.com/rhboot/shim-review/issues/591#issuecomment-6254988334)
was posted and reviewer acknowledgement is complete (`contacts verified OK`).

### Who is the secondary contact for security updates, etc.?

Emma Marsden, Star Labs, emma@starlabs.systems. Public PGP fingerprint:
0954B68651694FEAE5703B9B9F1033E526D797F4 (emma-marsden.asc).
Emma is the recovery custodian. Her [contact-challenge response](https://github.com/rhboot/shim-review/issues/591#issuecomment-6262351321)
was acknowledged by the reviewer.

### Were these binaries created from the 16.1 shim release tar?

Yes. Source SHA-256: 46319cd228d8f2c06c744241c0f342412329a7c630436fce7f82cf6936b1d603. Detached signature verification is recorded in source-signature.txt; the signature and Peter Jones’s public key are included.

### URL for a repo that contains the exact code which was built to result in your binary:

https://github.com/StarLabsLtd/shim-review/tree/starlabs-shim-x86_64-20261030

The tag includes the pinned Dockerfile, certificate, SBAT inputs and build logs.
BOOT-CHAIN-SOURCES.md and SOURCE-MANIFEST.json identify the downstream sources.

### What patches are being applied and why:

No shim source patches. The Dockerfile supplies the Vale certificate and SBAT, enables code-signing EKU support, sets systemd-bootx64.efi as the next loader, and enables NX plus PE-layout checks.

### Do you have the NX bit set in your shim? If so, is your entire boot stack NX-compatible and what testing have you done to ensure such compatibility?

Yes. Shim, systemd-boot, the dispatcher and the Normal UKI have NX_COMPAT.
Enforcing OVMF tests verified signatures and SBAT and loaded the Normal UKI
and embedded Linux through both loader routes. Unsigned, tampered, old-key and
targeted SBAT controls were rejected. See CANDIDATE-VALIDATION.md.

The earlier installer GUI and installed owner-session boots in VALIDATION.md
used the old certificate. The current tests cover authentication and loading;
the temporary firmware test signature is not Microsoft's.

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

Linux 7.2.8 contains the corresponding lockdown checks. The upstream source
URL and hash, Vale patches and exact configuration are listed in
BOOT-CHAIN-SOURCES.md; KERNEL-EVIDENCE.md records the source inspection.

### Is upstream commit [75b0cea7bf307f362057cc778efe89af4c615354 "ACPI: configfs: Disallow loading ACPI tables when locked down"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=75b0cea7bf307f362057cc778efe89af4c615354) applied?

Linux 7.2.8 contains the corresponding lockdown checks. See
BOOT-CHAIN-SOURCES.md and KERNEL-EVIDENCE.md for the source and configuration.

### Is upstream commit [eadb2f47a3ced5c64b23b90fd2a3463f63726066 "lockdown: also lock down previous kgdb use"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=eadb2f47a3ced5c64b23b90fd2a3463f63726066) applied?

Linux 7.2.8 contains the corresponding lockdown checks. See
BOOT-CHAIN-SOURCES.md and KERNEL-EVIDENCE.md for the source and configuration.

### How does your signed kernel enforce lockdown when your system runs with Secure Boot enabled?

CONFIG_SECURITY_LOCKDOWN_LSM_EARLY=y and CONFIG_LOCK_DOWN_KERNEL_FORCE_INTEGRITY=y.
The frozen Fedora guest recorded SecureBoot=1, SetupMode=0, integrity lockdown,
module sig_enforce=Y and SELinux enforcing. See KERNEL-EVIDENCE.md.

### Do you build your signed kernel with additional local patches? What do they do?

The frozen vale3 queue contains 24 patches for CFR/ACPI runtime transport,
encrypted hibernation, hardware support and DAVE sleep policy. The included
fedora/kernel-series and fedora/vale-kernel-patches.tar.gz identify the exact
queue; all patch bytes match source 80596e3 in the supplied audit. The later
authenticated-S4 size and worker-thaw fixes are not in this cohort.
BOOT-CHAIN-SOURCES.md identifies the upstream source, exact patches and final
kernel configuration, and supplies selected systemd/recovery/UKI sources.

### Do you use an ephemeral key for signing kernel modules?

Yes. Each full RPM build unpacks a fresh kernel tree. Linux generates
certs/signing_key.pem, embeds its public certificate and signs modules during
modules_install using SHA-512. The recipe does not import, export or package
the private key. Build logs record key generation and removal of the build tree
after packaging. MODULE-KEY-LIFECYCLE.md gives the recipe and evidence.

### If not, please describe how you ensure that one kernel build does not load modules built for another kernel.

Separate fresh builds generate different signing authorities; this recipe
does not reuse prior private keys or add prior build certificates as extra
built-in trust. Compatible vermagic/ABI alone therefore does not authenticate
another build's module. CONFIG_MODULE_SIG_FORCE is unset, but the recorded
guest had sig_enforce=Y and integrity lockdown: the trusted control loaded,
while identical unsigned and valid untrusted-signed controls were rejected.
See MODULE-KEY-LIFECYCLE.md and KERNEL-EVIDENCE.md.

### If you use vendor_db functionality of providing multiple certificates and/or hashes please briefly describe your certificate setup.

One Vale code-signing certificate, with no allow-listed binary hashes. Public DER: vale-secure-boot.der.

### If there are allow-listed hashes please provide exact binaries for which hashes are created via file sharing service, available in public with anonymous access for verification.

None.

### If you are re-using the CA certificate from your last shim binary, you will need to add the hashes of the previous GRUB2 binaries exposed to the CVEs mentioned earlier to vendor_dbx in shim. Please describe your strategy.

First application; no previous Vale shim/GRUB2 release to revoke.

### Is the Dockerfile in your repository the recipe for reproducing the building of your shim binary?

Yes: docker build . uses the submission-root Dockerfile. The base image, package snapshot and release archive are pinned. Two independent local builds reproduced all three EFI binaries byte-for-byte; see reproducibility.txt.

### Which files in this repo are the logs for your build?

build-first.log, build-second.log and build-packages.txt.
Source verification: source-signature.txt.
Current runtime checks: CANDIDATE-VALIDATION.md and normal-chain-evidence.json.
Historical runtime checks: VALIDATION.md.

### What changes were made in the distro's secure boot chain since your SHIM was last signed?

First application.

### What is the SHA256 hash of your final shim binary?

6ee83a224ff0a1490a54795e668f0ac669098944eaef6abc4fc5fdd581c2e275

Unsigned; awaiting upstream acceptance and Microsoft signing.

### How do you manage and protect the keys used in your shim?

The RSA-4096 key was generated in a YubiHSM 2 FIPS 140-3 in approved mode.
Signing uses a separate YubiKey, password and touch. Administrator access is
held separately for provisioning and recovery. Only the public DER is included
in shim and the build inputs.

Two encrypted wrapped-key backups are stored separately. Sean and Emma each
hold one recovery share. Recovery was tested with a disposable key, and both
production backup copies were verified. See KEY-PROTECTION.md.

### Do you use EV certificates as embedded certificates in the shim?

No. The embedded Vale certificate is separate. An EV certificate for signing
Microsoft submission CABs is registered and active in Partner Center. It is not
the Vale Secure Boot certificate embedded in shim.

### Are you embedding a CA certificate in your shim?

No. It is a self-signed RSA-4096 code-signing leaf with CA:FALSE, valid from
29 October 2026 to 29 October 2031. Public DER SHA256:
d247d96c7dec493c3b89749e02852f032c247c9da80407fbdccaf2db51381b61.

Replacing the certificate requires a new reviewed shim and corresponding
downstream signatures.

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
Signed loader SHA256:
206b229f2af8b094cbbb08d6520138efe3644274620ab74d228004076430378c.

Source and package bindings are in BOOT-CHAIN-SOURCES.md, SOURCE-MANIFEST.json
and fedora/source-freeze.json. candidate-signature-evidence.json records the
original and re-signed identities and unchanged payloads.

### If your shim launches any other components apart from your bootloader, please provide further details on what is launched.

The default next loader is systemd-bootx64.efi. We also test the signed recovery
dispatcher at that filename. Both routes verify and load the same Normal UKI;
for the dispatcher test it is placed at EFI/Vale/Normal.efi. This tests recovery
authentication routing, not installer functionality.

### If your GRUB2 or systemd-boot launches any other binaries that are not the Linux kernel in SecureBoot mode, please provide further details on what is launched and how it enforces Secureboot lockdown.

systemd-boot and the recovery dispatcher load signed UKIs containing the
kernel, initrd and command line. The inspected boot surfaces contain no other
EFI updater or utility. Component identities and SBAT rows are in SBAT.md;
verification results are in CANDIDATE-VALIDATION.md.

### How do the launched components prevent execution of unauthenticated code?

Firmware verifies shim; shim verifies Vale-signed systemd-boot; systemd-boot verifies the UKI before execution. Kernel integrity lockdown and module-signature verification protect kernel execution.

### Does your shim load any loaders that support loading unsigned kernels (e.g. certain GRUB2 configurations)?

The tested chain refuses unsigned, tampered and old-key UKIs. Valid signed
UKIs are also refused under the targeted SBAT revocation policy.
See CANDIDATE-VALIDATION.md and normal-chain-evidence.json.

### What kernel are you using? Which patches and configuration does it include to enforce Secure Boot?

Linux 7.2.8-vale3 / kernel-vale-7.2.8-3.vale.fc44.x86_64, built from Linux 7.2.8
plus the included frozen 24-patch queue and Fedora configuration. The exact
packaged config is fedora/kernel-config. Integrity lockdown is forced and
modules are signed; runtime identity and controls are in KERNEL-EVIDENCE.md.
The upstream source, exact patches and configuration are linked in
BOOT-CHAIN-SOURCES.md.

### What contributions have you made to help us review the applications of other applicants?

None claimed in this application.

### Add any additional information you think we may need to validate this shim signing application.

Both security contacts are verified. Hardware-backed signing, backup checks
and the Normal boot-chain authentication tests are complete. Build, source,
key-custody and module-key details are included in this packet.

Vale is still in development. Upstream technical acceptance and Microsoft
signing remain pending; no CAB has been submitted. The returned Microsoft-signed
binary will be checked before deployment. Historical Ubuntu evidence is kept
in HISTORICAL-UBUNTU-KERNEL.md and HISTORICAL-UBUNTU-VALIDATION.md.
