Vale shim 16.1 — signing application

Build (public inputs only):
  podman build --no-cache -f Dockerfile -t vale-shim .
  mkdir -p export
  podman run --rm --network none -v "$PWD/export:/export:rw" vale-shim sh -c "cp /output/* /export/"

shimx64.efi is the unsigned binary for eventual Microsoft signing.
mmx64.efi and fbx64.efi are reproducible unsigned companions, not shipping binaries.
No private keys or temporary QEMU-signed shim are included.
The source archive digest is pinned in the Dockerfile; its detached signature
and the upstream public key are included alongside the verification log.
See CANDIDATE-VALIDATION.md for current tests and PREPARATION.md for submission
status. VALIDATION.md contains the original-certificate Fedora results.
