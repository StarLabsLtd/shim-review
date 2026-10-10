# Vale shim-review preparation — reconciled 10 October 2026

Local draft, not published or submitted. Hardware enrollment and Certum EV
registration are approved. Do not upload the Vale private key: the submission
artifact is the unsigned shim embedding its public certificate.

## Selected review-evidence cohort

- Fedora image `202610061000`, source
  `80596e30435c4d1845bb52319aeb50de2907da0f`, kernel `7.2.8-vale3`, systemd
  `259.5-2.vale.fc44`. This is a frozen tested cohort, not a claim that later
  image 1915 or K7/S4 work passed the same shim-chain tests.
- Shim remains SHA-256
  `e0fa2d72123cd218ac25c1d46ea991ffe7f1cea7cbe597b87a04dd28cc4df7c7`.
  Exact submitted-body binding to the temporarily signed tested file is
  included in fedora/shim-body-binding.json.
- VALIDATION.md and KERNEL-EVIDENCE.md reconcile the Linux lane's completed
  enforcing-OVMF boot/refusal/module evidence. No new tests were run here.
- Historical Ubuntu records remain separate and unchanged. Existing shim
  build/reproducibility records and unsigned binaries are unchanged.
- The frozen ISO/ESP do not ship shim. Returned Microsoft-signed shim and
  installer/installed/update integration need later qualification.

## Remaining local preparation

1. Finish publicly accessible complete source/build references and the raw
   evidence set for the frozen cohort. Included source snapshots, patch archive,
   configuration, certificate and summaries are not a substitute for resolving
   every public-source field. Original raw evidence is retained locally under
   the sibling shim-fedora-20261010 directory; copied receipts retain their
   original path references and should not be mistaken for standalone archives.
2. Complete independent review of this reconciliation before public release.

## Owner/contact facts still open

- Sean owns updates; Emma is secondary contact. Both public PGP keys are included.
  Emma's encrypted key bundle was emailed; successful import/decryption is not
  confirmed. Confirm use of Sean's existing PGP key for review correspondence
  and name emergency-revocation/backup-contact responsibilities.
- Sean agreed the planned policy: two encrypted removable copies in locked
  offsite storage, one brought in for signing sessions. KEY-PROTECTION.md records
  requirements, not operational attestation. Keep that status truthful.
- Confirm the ten-year leaf-certificate rationale and production rotation roles.
  Resolve module-key lifecycle separately; build-tree removal is not secure
  erasure or proof of no remaining copy/cross-build trust.
- Establish the actual registered EV token/cloud signing route. Registration
  is complete; it does not itself demonstrate ability to sign the CAB.

## External review/signing sequence

When the completed packet is authorized for publication, publish the frozen
repository/tag and open the shim-review issue. Both contacts must complete
reviewers' PGP email challenges; obtain the accepted label.

Only then EV-sign the adjacent flat vale-shim-x64-unsigned.cab, currently unsigned
and containing only the exact shimx64.efi, using the registered organization
certificate. Verify the CAB signature and submit through Partner Center File
Signing. MokManager/fallback are unsigned review companions, not Microsoft
signing requests or already shipped utilities. Retain returned binaries and
qualify their exact chain on firmware trusting Microsoft UEFI CA 2023.

Requirements:

- https://github.com/rhboot/shim-review/blob/main/README.md
- https://learn.microsoft.com/en-us/windows-hardware/drivers/dashboard/file-signing-reqs
