# Final vale3 runtime/module-trust reconciliation

**PASS for the exact installed guest cohort and three module controls.** The final evidence freeze records a passing runtime/SHA256SUMS verification for all 304 listed files. This is an isolated guest result, not physical hardware or S4 acceptance. No VM interaction or additional cases were performed during reconciliation.

- Source/image: 80596e30435c4d1845bb52319aeb50de2907da0f, installed 202610061000 UKI SHA256 d0a0cc13655fad321e305afd6578002a9d9d52303a11db67055c0a95eda7698b; actual kernel 7.2.8-vale3 / 7.2.8-3.vale.fc44.
- Guest kernel notes decode to build ID **1cc3bbb841357db5d343b2e094bc59a27c34d4a7**, matching the exact RPM/UKI kernel ELF. Config SHA256 cf562eb28f2c448fea09b1f2e39922336fef06f589db923e4b90002bdb671a00 matches the RPM extraction.
- Boot ID 777f69d2-8a17-4aa5-99a9-11be07c391eb; SecureBoot=1, SetupMode=0, integrity lockdown, sig_enforce=Y, SELinux Enforcing.
- Original signature crypto verified against recovered public certificate SHA256 169b9ad28e02eefa597b11f276c68bd2c8ea97024cb7cea52284c95023e8973d. Runtime Loaded X509 and /proc/keys describe its **SKI 8d9f7b2d135274b2b4b2076b2d043e3d200bf9b9**. Module modinfo identifies the same certificate by **serial 5efcfa981a570885f5b8b9c95aecdbf77c706bdc**. Linux x509_key_preparse prefers SKI in the key description; PKCS7 issuer/serial identifies this signature. No identity discrepancy remains. keyctl was absent; no full per-keyring traversal is claimed.

| Same-payload dummy control | Actual observed outcome |
|---|---|
| Original signed | insmod exit 0, module present; unload exit 0 |
| Signature removed | insmod exit 1; “Key was rejected by service”; kernel “Loading of unsigned module is rejected”; absent |
| Valid disposable untrusted signature | insmod exit 1; “Key was rejected by service”; kernel “Loading of module with unavailable key is rejected”; absent |

These are insmod process exit statuses and messages, **not a syscall errno trace**. All three delivered hashes match the local manifest and used the same var_run_t context without relabeling. Final cleanup records dummy absent. The valid untrusted signature was independently verified offline; its rejection demonstrates unavailable trust rather than broken fixture cryptography. Actual sig_enforce=Y selects the explicit signature-enforcement rejection path even though CONFIG_MODULE_SIG_FORCE is unset; existing IMA Secure Boot architecture policy enables enforcement.

Frozen cleanup receipts record that owned QEMU/swtpm processes were stopped, owned /tmp/vsf-20261010 scratch was removed, and the original disk/ISO/paired source state was preserved. Public certificate and reconstruction records remain under kernel-audit/module-controls. Production private keys were neither read nor changed. Build-tree removal evidence establishes ordinary generated-key removal from that tree, not an additional secure-erasure/custody policy.

Final freeze anchors:

- runtime/REPORT.txt: 56fb29d52e5c0bf581d82621dc529714c85a9a11c4c191e364e09c358478bdf9
- runtime/CASE-RESULTS.json: 2076630d88a97906f2af41fc856e3acb200cdfdcc291ad6586188e46e919c74b

Exact raw-log hashes and decoded binding are in module-controls/runtime-reconciliation.json. Full frozen manifest check command/results are in frozen-runtime-hash-verification.json. This closes the narrow current-kernel identity and three-control reconciliation; it does not change the separate source-only S4 constraints in REPORT.md.
