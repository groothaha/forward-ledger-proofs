# forward-ledger-proofs

Timestamp proofs for a private, forward-running paper-trading record. This repository holds hashes and third-party timestamp tokens only. It does not contain the records or the rules.

Three times per trading day (Korea time) a manifest is written. It lists the SHA-256 of every file in the record at that moment and includes the SHA-256 of the previous manifest, so the manifests form a chain. The manifest's own SHA-256 is then timestamped by:

- freetsa.org (RFC 3161): `*.freetsa.tsr`
- DigiCert (RFC 3161): `*.digicert.tsr`
- OpenTimestamps (Bitcoin): `*.ots`

`CHAIN.log` has one line per manifest: `seq local_time kind date manifest_sha256 prev_manifest_sha256`.

## Verifying without the manifests
```
H=<manifest_sha256 from CHAIN.log>
openssl ts -verify -digest $H -in proofs/<date>/<seq>_<kind>.json.freetsa.tsr \
  -CAfile tsa_certs/freetsa_cacert.pem -untrusted tsa_certs/freetsa_tsa.crt
openssl ts -reply -in proofs/<date>/<seq>_<kind>.json.freetsa.tsr -text | grep 'Time stamp'
ots verify -d $H proofs/<date>/<seq>_<kind>.json.ots
```
When the manifests and record files are shared privately, anyone can recompute their hashes and match them to this chain.
