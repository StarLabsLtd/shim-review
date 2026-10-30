# Kernel module-signing key lifecycle

This answer concerns the frozen `kernel-vale-7.2.8-3.vale.fc44.x86_64`
cohort, not the separate Vale EFI signing authority or Certum EV CAB key.

## Build-local key

The selected RPM recipe starts with `%setup -q -n linux-%{version}`. It does
not use RPM's `-D` option to preserve a previous build directory. Each full
build therefore unpacks a fresh source tree; resuming an existing build tree
is not a new independent build.

The final configuration sets `CONFIG_MODULE_SIG_KEY="certs/signing_key.pem"`,
RSA, SHA-512 and `CONFIG_MODULE_SIG_ALL=y`; `CONFIG_SYSTEM_TRUSTED_KEYS` is
empty. The recipe supplies no private module key. Linux generates the
build-local key and embeds its public certificate in the kernel.
`modules_install` signs that build's modules with that key. The recipe removes
the installed build/source links and packages the matching kernel, modules,
configuration and System.map; it has no private-key export or packaging step.

The retained build excerpt records `GENKEY certs/signing_key.pem` at line 1582
and removal of the generated-key build tree during `rmbuild` at lines
57876–57881. The lifecycle is generation for a fresh build, signing during
installation, then ordinary build-tree removal. It is not a long-lived key
reused by this recipe to sign modules for independent builds.

The recorded cleanup is ordinary build-tree removal, not forensic erasure.

## Cross-build trust

Fresh full builds generate a different signing authority. A module signed by
another build is consequently not authenticated by this kernel's build-local
certificate merely because it has compatible vermagic/ABI. ABI checks are not
the trust boundary. The selected recipe does not reuse another build's private
key or add previous build certificates as additional built-in authorities.

The existing enforcing guest accepted the original trusted module and rejected
the identical unsigned and valid untrusted-signed payloads. Runtime
`sig_enforce=Y` and integrity lockdown were recorded even though
`CONFIG_MODULE_SIG_FORCE` is unset. These tests demonstrate module-signature
enforcement. They did not compare two independently built kernels. Enrolling
another key changes the machine's trust policy.

No retained private key is needed to run already signed modules. Additional
modules are not signed through a persistent module-signing service in this
recipe. A changed module set requires a matching kernel/modules build, rather
than reusing a previous build's signing key. Re-signing the outer EFI/UKI
container with the HSM does not change this module authority or require a
kernel rebuild.

## Exact evidence already in the packet

| Input / record | Identity or role |
| --- | --- |
| `fedora/source-freeze.json`, `shipping_like_source_configs["kernel/fedora/kernel-vale.spec"]` | Full recipe; SHA-256 `d5c8c24634c24269d945fefa9a83d310d0c9f6009018c27ab456041c3d4510db` |
| `fedora/native-kernel-source-binding.json` | Actual producer commit `c98ff436f79a8deed4bce090041805a54b1fccd3`; same recipe identity |
| `fedora/kernel-config` | SHA-256 `cf562eb28f2c448fea09b1f2e39922336fef06f589db923e4b90002bdb671a00` |
| `fedora/module-key-build-lifecycle.json` | Existing generation and ordinary removal excerpts |
| `fedora/vale3-module-public.der` | Public certificate SHA-256 `169b9ad28e02eefa597b11f276c68bd2c8ea97024cb7cea52284c95023e8973d` |
| `fedora/module-runtime.md` and `KERNEL-EVIDENCE.md` | Exact running-kernel binding and three module controls |

The kernel source and exact Vale patch queue are identified in
BOOT-CHAIN-SOURCES.md. Linux's `certs/Makefile`, `scripts/Makefile.modinst` and
`kernel/module/signing.c` implement generation, signing and trust enforcement.

Consumer references: [RPM setup semantics](https://rpm.org/docs/4.20.x/manual/spec.html#setup),
[Linux module signing](https://docs.kernel.org/admin-guide/module-signing.html).
