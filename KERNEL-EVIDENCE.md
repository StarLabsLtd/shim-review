# Frozen Fedora kernel and module trust

Kernel: `7.2.8-vale3`, RPM `kernel-vale-7.2.8-3.vale.fc44.x86_64` SHA-256
`e6c7955c8b7a0e4e94c9fe466c67cdcc0e03566eba0a0381eed87107e97b58a1`.
Image source is `80596e30435c4d1845bb52319aeb50de2907da0f`; native kernel
producer source is `c98ff436f79a8deed4bce090041805a54b1fccd3`.
fedora/native-kernel-source-binding.json identifies the actual producer inputs;
fedora/kernel-source-binding.json binds all 24 patch bytes to the image source.
These are different provenance roles, not interchangeable source commits.

## Included public inputs

- fedora/kernel-config is the exact RPM's final configuration, SHA-256
  `cf562eb28f2c448fea09b1f2e39922336fef06f589db923e4b90002bdb671a00`.
- fedora/kernel-series identifies the 24-patch queue; its archive
  fedora/vale-kernel-patches.tar.gz has SHA-256
  `4e2f2e40f270ae850d1b09620f61bfed0368677b3c1dc11744afa46b396435fe`.
- fedora/source-freeze.json retains the image lock, kernel spec/fragments/series,
  systemd spec/SBAT patch and recovery-dispatch/image recipes as inspected.
  It includes other lane heads for context; those heads are not this image.
- Linux source archive is https://cdn.kernel.org/pub/linux/kernel/v7.x/linux-7.2.8.tar.xz,
  SHA-256 `65a07d5d0d88dac963b190f087ccd1498622cb742ccfa1b4e46f8110f08832a0`.

BOOT-CHAIN-SOURCES.md supplies the focused source references. Existing source
inspection confirms the EFI SSDT, ACPI configfs and prior-KGDB lockdown checks
asked about by shim-review. Forced integrity lockdown is enabled early;
module signing, SHA-512/RSA, strict devmem and kexec verification are enabled.
CONFIG_MODULE_SIG_FORCE is unset. Runtime sig_enforce=Y is established by the
guest evidence; the audit attributes enablement to IMA Secure Boot architecture
policy. Lockdown is forced independently of firmware Secure Boot state.

## Exact running kernel and three controls

Runtime kernel ELF build ID `1cc3bbb841357db5d343b2e094bc59a27c34d4a7` matches
the exact RPM and current installed UKI. Recovered module public certificate
fedora/vale3-module-public.der has SHA-256
`169b9ad28e02eefa597b11f276c68bd2c8ea97024cb7cea52284c95023e8973d`.
Its SKI is `8d9f7b2d135274b2b4b2076b2d043e3d200bf9b9`; its serial is
`5efcfa981a570885f5b8b9c95aecdbf77c706bdc`. Runtime key descriptions use SKI,
whereas the module signature identifies issuer/serial; there is no discrepancy.

The same dummy module payload was exercised three ways: original signed
loaded/unloaded successfully; unsigned and valid untrusted-signed variants
were rejected and stayed absent. Crypto verification established that the
untrusted fixture was validly signed, not malformed. Kernel messages explicitly
identified unsigned/unavailable-key rejection. These are insmod exit statuses
and messages, not traced syscall errno values. keyctl was unavailable; a full
per-keyring traversal is not claimed. See fedora/module-runtime.md.

## Lifecycle and scope

MODULE-KEY-LIFECYCLE.md resolves the selected build's lifecycle from its exact
recipe and consumers: each full build unpacks fresh sources, generates a
build-local key, signs its modules, packages no private key and removes its
build tree during rmbuild. No private-key import/export or reuse step exists
in that recipe. Independent fresh builds therefore have different signing
authorities; compatible ABI alone does not authenticate another build's module.
Existing generation/removal excerpts and trusted/untrusted module controls
support this answer. It is not a claim of forensic erasure, absence of every
possible host backup or newly executed cross-build validation.

The exact vale3 S4 queue does not contain the later authenticated-size guard
and worker-thaw fixes. That source limitation and full DAVE/S4 release acceptance
are separate from the recorded boot/module results. No private signing material
was inspected or added to this packet. Historical Ubuntu vale1 evidence remains
unchanged in HISTORICAL-UBUNTU-KERNEL.md.
