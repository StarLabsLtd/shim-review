# Submission status

The RSA-4096 candidate is published at
[starlabs-shim-x86_64-20261030](https://github.com/StarLabsLtd/shim-review/tree/starlabs-shim-x86_64-20261030).
The same binary was first published in commit
`c8ac2e6c7f4aa0ba6102198bac1a9b1e8b1ae975`.

Unsigned shim SHA256:
`6ee83a224ff0a1490a54795e668f0ac669098944eaef6abc4fc5fdd581c2e275`.

Both security contacts are verified in
[issue #591](https://github.com/rhboot/shim-review/issues/591).
Upstream technical acceptance and Microsoft signing remain pending.

The packet contains the reproducible shim build, public certificate, boot-chain
source references, module-key lifecycle and Normal UKI verification results.
See README.md, BOOT-CHAIN-SOURCES.md, KEY-PROTECTION.md and
CANDIDATE-VALIDATION.md.

## Next steps

1. Obtain upstream technical acceptance for the exact shim binary.
2. Put only that binary in a flat CAB, sign the CAB with our separate EV
   certificate and verify it.
3. Submit the CAB through Partner Center UEFI File Signing Services.
4. Verify Microsoft's returned signature and bind the returned binary to the
   accepted unsigned input.
5. Test the returned binary before integrating it into Vale's ESPs, installer
   media and updates.

Record the accepted revision, unsigned shim hash, signed CAB hash, submission
ID and returned artifact hashes. No CAB has been submitted.

Microsoft's `.sbom` wording remains an open clarification. Upstream
[shim issue #788](https://github.com/rhboot/shim/issues/788#issuecomment-4457092648)
closed in May 2026 with the conclusion that it was not then enforced for shim.
We have not added an SBOM section.

References: [shim submission instructions](https://github.com/rhboot/shim-review/blob/main/docs/submitting.md),
[reviewer guidelines](https://github.com/rhboot/shim-review/blob/main/docs/reviewer-guidelines.md),
[Microsoft CAB requirements](https://learn.microsoft.com/en-us/windows-hardware/drivers/dashboard/file-signing-reqs)
and [submission workflow](https://learn.microsoft.com/en-us/windows-hardware/drivers/dashboard/file-signing-manage).
