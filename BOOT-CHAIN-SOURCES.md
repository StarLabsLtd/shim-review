# Boot-chain sources

The downstream inputs use Fedora 44 source
`80596e30435c4d1845bb52319aeb50de2907da0f`, also used for the original
`starlabs-shim-x86_64-20261015` tests. SOURCE-MANIFEST.json identifies all
50 exported source files by Git blob and SHA256. The two source archives
are unchanged.

## Shim

The [RSA-4096 candidate](https://github.com/StarLabsLtd/shim-review/tree/starlabs-shim-x86_64-20261030)
includes the pinned Dockerfile, public certificate, SBAT inputs, source-signature
verification, build logs and reproducibility results.
The shim 16.1 source archive is
<https://github.com/rhboot/shim/releases/download/16.1/shim-16.1.tar.bz2>,
SHA-256 `46319cd228d8f2c06c744241c0f342412329a7c630436fce7f82cf6936b1d603`.
No shim source patches were applied. The replacement changes only the embedded
public certificate, with source, toolchain and SBAT preserved. Its
unsigned SHA256 is
`6ee83a224ff0a1490a54795e668f0ac669098944eaef6abc4fc5fdd581c2e275`.
The fixed 50-file source manifest and both source archives remain unchanged.

## systemd-boot, systemd-stub and UKI tooling

The selected build is `259.5-2.vale.fc44`. Its upstream source is
<https://archive.ubuntu.com/ubuntu/pool/main/s/systemd/systemd_259.5.orig.tar.gz>,
SHA-256 `80ed55a8a69c4bd1fb12a36659303372b37baf9ee224ef4f032db4b748be0f76`.
This digest was checked against the retained upstream source archive.

`systemd-package-4746c08.tar` supplies the RPM spec, auxiliary RPM sources and
the only two patches selected by that spec:

1. `0001-sleep-Allow-userspace-hibernation-backends.patch` changes the userspace
   sleep backend.
2. `0002-boot-add-Vale-SBAT-entries.patch` adds the Vale EFI SBAT rows while
   retaining upstream/distribution rows.

The export is from the producer commit
`4746c08526eaf1e9ec5c171a8678acecfead4bbb`, tree
`f6825bbf214c5cebf4d69e9ef85f9070ca654ed1`, identified by the retained native
tools receipt. All 25 exported package files match the retained source RPM's
payload byte-for-byte. The source RPM SHA-256 is
`987e62bdc0a91c108062070d0c61008ae6c8c97d3ae01382b423549e7a426cc8`.

The resulting unsigned boot RPM SHA-256 is
`ec4a55646d6be36bef732fc88a1c3a847485e13331106140476b6f23ebd2a802`,
matching the frozen shipping inventory. Its unsigned loader SHA-256 is
`551049e65cb130817d83fb895271fa49546725954e9f6acb9a6d77b575f8fc98`;
the unsigned linux stub is
`d9083309c6908a1e218bc5e8e49667549ccd9e6a46567240e89e18dfbfb8d782`.
The original installed signed loader was
`ba71acffa69d2712a0e5ba6771489f9d4363a08e5a06000f507e1df5b421dda6`.
The same loader signed with the RSA-4096 key is
`206b229f2af8b094cbbb08d6520138efe3644274620ab74d228004076430378c`;
its payload is unchanged.

The same producer receipt identifies native ukify SHA-256
`5fa39e4aab9d64a2313f8f57b65f5351eb56428f8f69dced2a49b5a6ad5abf90`
and systemd-measure SHA-256
`04fd3bd087fdb1128b41f4fc842d427773e50bf9c3d889f7605eb8892df3f594`.
These match `image/fedora44/ukify.lock` in the boot-context export.

## Custom recovery dispatcher and UKI construction

`vale-boot-context-80596e3.tar` exports selected files from frozen cohort
commit `80596e30435c4d1845bb52319aeb50de2907da0f`, tree
`26a882100793ac8058e2066991971f9090a812c2`. It includes:

- The MIT-licensed recovery dispatcher source and its existing build/tests.
- Its pinned shim/GNU-EFI SDK Dockerfile and build script.
- UKI construction and inspection scripts, native-tool lock and SBAT inputs.
- The selected EFI assembly scripts and Secure Boot settings.

The dispatcher chooses the Dave or Normal signed recovery UKI using the exact
SMBIOS BIOS-vendor rule, then invokes firmware `LoadImage`/`StartImage`.
An authentication failure is returned without trying a different target.
The dispatcher source was introduced by
`f0521b6ef92f0a8fbafaa7a65714036c5edf6b5b` and is exported here from the frozen
cohort tree. The existing inventory records its signed EFI hash as
`f7188dbec3bbe42f9413c79f4435949b65fd179381b5f2ac9dd732c191df3879`.
The dispatcher was re-signed without rebuilding its payload. Its new signature
and payload comparisons are in candidate-signature-evidence.json.

The UKIs embed the kernel, initrd and command line and carry separate
systemd-stub and `uki.vale` SBAT rows. The existing tagged `VALIDATION.md`,
`SBAT.md` and Fedora evidence describe their identities and the boot/refusal
cases. The source export provides the construction and validation code used by
the frozen cohort, rather than all unrelated Vale image packages.

## Linux and module trust

The kernel source is
<https://cdn.kernel.org/pub/linux/kernel/v7.x/linux-7.2.8.tar.xz>, declared
SHA-256 `65a07d5d0d88dac963b190f087ccd1498622cb742ccfa1b4e46f8110f08832a0`.
The packet supplies the complete 24-patch Vale queue,
`fedora/kernel-config`, `kernel-series`, source bindings and runtime summary.
The EFI certificate change does not rebuild Linux or change its module authority.

MODULE-KEY-LIFECYCLE.md traces the already supplied RPM recipe: fresh source
unpacking, generated build-local signing key, automatic module signing, no
private-key packaging/export step and ordinary build-tree removal. Separate
fresh builds do not share this signing authority. The existing installed guest
accepted the trusted module control and rejected matching unsigned and valid
untrusted-signed controls. These results establish the selected kernel's
module-signature enforcement for that kernel and configuration.

## Submission status

Both contacts are verified in [issue #591](https://github.com/rhboot/shim-review/issues/591).
Upstream technical acceptance and Microsoft signing remain pending.
See PREPARATION.md for submission status and KEY-PROTECTION.md for key custody.
