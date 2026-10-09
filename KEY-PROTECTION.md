# Vale production signing-key requirements

Status: proposed operating policy, not an attestation of deployed controls.
Sean has specified two encrypted removable copies, locked offsite storage,
and bringing one copy to the office only when signing is needed. The controls
below must be implemented and checked before production signing or describing
them as operational in the shim-review application.

## Custody and storage

- Sean owns production signing and security updates. Name an authorized backup
  custodian and establish emergency-revocation authority before release; Emma's
  role as secondary security contact does not itself grant private-key access.
- Keep two independently encrypted removable copies in locked offsite storage,
  preferably at separate sites to avoid one incident destroying both. Bring
  only one copy into the office for a signing session; keep the other offsite.
- Use maintained full-volume encryption with a strong, unique unlock secret.
  Store recovery/unlock material separately from the media, in a restricted
  recovery arrangement available to the named custodian. Two encrypted copies
  are not a usable backup if both depend on a lost password.
- Track each copy by an internal identifier, authorized custodians and checkout
  records. Do not put storage addresses, unlock secrets or private keys in the
  public review repository. Keep media encrypted while stored or transported.
- Do not retain working private-key copies on the build machine, in its backups,
  containers, CI, repositories, artifact bundles or cloud-sync folders. Inventory
  any existing copies and resolve them before claiming offsite-only custody.

## Signing session

1. Build without the private key. Freeze the reviewed source revision and an
   unsigned artifact manifest before attaching the signing media.
2. Check that the build/signing machine is updated, access-controlled and trusted.
   Stop untrusted workloads and remote access for the session; disconnect the
   network where practical. Do not sign arbitrary caller-supplied files.
   Encryption protects a locked disk, not a key exposed to a compromised host.
3. Bring in one copy, unlock it only for the session, and restrict its mount to
   the signing operator. Use a read-only mount where the signing tool supports
   it. Reference the key on the media rather than copying it to local disk.
4. Check the artifact hashes against the approved manifest. Sign only the
   authorized release components; never log secrets or enable shell tracing
   around unlock/signing operations. Prevent secret-bearing core dumps and
   persistent swap/hibernation exposure during signing.
5. Verify every resulting signature against the expected public certificate.
   Record source revision, unsigned and signed hashes, certificate fingerprint,
   operator, date and purpose in an access-controlled signing log.
6. Unmount and lock the encrypted volume, disconnect the media and return it to
   locked offsite storage. Shut down the signing host after the session to end
   the unlocked-key exposure window. Do not claim this proves forensic erasure.

If the build machine cannot be trusted or isolated adequately, use a dedicated
offline signing machine or a suitable hardware signer instead. Do not weaken
the above controls merely to automate unattended signing.

## Backup, recovery and compromise

- Before first production use, test recovery from each copy in a controlled
  session: unlock it, confirm that its public key matches the intended Vale
  certificate, and sign/verify a non-release test artifact. Repeat after any
  backup replacement or credential change, and at least annually.
- Replace failing media promptly while maintaining two verified copies. Check
  both copies and the recovery arrangement before retiring the old media.
- On lost media, suspicious signing activity or possible host compromise, stop
  signing and preserve the relevant logs. Sean coordinates investigation;
  notify the secondary contact and relevant shim/Microsoft security channels.
- Do not restore service by simply reusing a possibly exposed key. Prepare a
  replacement authority, assess affected artifacts, and coordinate replacement
  shim, signed updates and appropriate SBAT/dbx revocation with reviewers and
  Microsoft. Test recovery before withdrawing trust needed by existing users.
- The certificate's ten-year validity is not a promise to use the key for ten
  years. Review it annually and on incidents; plan migration before expiry.
  The final lifetime rationale and distribution/revocation mechanism must be
  included in the submission, not inferred from this policy alone.

## Separate authorities and release gate

The Vale Secure Boot key, kernel-module signing keys and the Certum EV CAB
signer have different purposes. Record each authority's fingerprint, custodian,
lifecycle and recovery process separately. Follow the EV provider's token/cloud
signing requirements; this policy does not export or relocate its private key.
Resolve the module-key lifecycle independently: kernel autogeneration does not
prove that its key is ephemeral or that cross-build module loading is prevented.

Before calling these controls operational, record that both media copies and
recovery tests exist, custody and emergency roles are assigned, no unintended
private-key copies remain, and a complete signing-session rehearsal passed.
Keep that internal checklist and any sensitive evidence outside the public
packet. Only publish an accurate summary of controls actually implemented;
label future controls as planned.
