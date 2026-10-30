# Vale signing keys

Sean Rhodes is responsible for signing, security updates and revocation.
Emma Marsden is the secondary security contact and recovery custodian.

## Signing

The RSA-4096 key was generated inside a YubiHSM 2 FIPS 140-3 running firmware
2.4.1 in approved mode. It permits signing and encrypted wrapped backup.

Signing uses a non-exportable YubiKey HSM Auth credential, a separate password
and touch. The signing role can sign in its assigned domain and cannot delegate
access. Administrator access is held separately for provisioning and recovery.

We build without the private key. Before signing, we check each input against
its recorded hash. Afterwards, we verify the signature and confirm that the
payload sections, SBAT and NX settings are unchanged. The HSM is disconnected
and stored securely when unused. Passwords and recovery shares are kept out of
build inputs, source repositories and logs.

## Certificate

The self-signed certificate uses RSA-4096 and SHA256, with CA:FALSE,
digitalSignature and codeSigning usage. It is valid from 29 October 2026
22:31:38 UTC to 29 October 2031 22:31:38 UTC.

Public DER SHA256:
`d247d96c7dec493c3b89749e02852f032c247c9da80407fbdccaf2db51381b61`.

Only the public certificate is embedded in shim. Replacing it requires a new
reviewed shim and corresponding downstream signatures.

## Backup and recovery

Two encrypted USB drives hold identical wrapped-key backups and public
certificate records. They are kept in separate locked offsite locations.
Sean and Emma each hold one recovery share, separately from the drives.
Recovery requires both shares and either backup drive. The two-share split is
implemented by Yubico's recovery software.

Recovery was tested with a disposable RSA-3072 key: we removed the test key and
wrapping key, reconstructed the wrapping key from both shares, restored the
signing key from USB, checked its public identity and signed again through the
YubiKey. The production RSA-4096 key uses the same backup and recovery procedure. Both
production backup copies were written, synced and read back with matching
checksums; the production key was not deleted for the test.

If the HSM fails, we will recover onto a compatible approved replacement.
Until then, signing stops. Recovery material remains protected after certificate
expiry until retired.

## Incidents

On suspected compromise or key/device loss, we stop signing and contact the
shim and Microsoft security teams. Sean coordinates affected-artifact checks,
revocation and replacement.

The kernel module-signing key and the Certum EV certificate used for Microsoft
submission CABs are separate authorities. See MODULE-KEY-LIFECYCLE.md for the
module-signing process.
