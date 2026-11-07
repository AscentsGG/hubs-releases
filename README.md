# Hubs release statements

Every web release of [Hubs](https://thehubs.gg) adds one signed statement here:

- `releases/<release>.json` lists every file the release serves on thehubs.gg, with its SHA-256, and the headers that decide what a page may load and run.
- `releases/<release>.json.sig` is the Ed25519 signature over the exact bytes of that file, in base64.

Statements are never edited after they are signed. The first is release `20261106T140852Z-a32e3272dec5`.

## App releases

A Mac or Windows release whose encryption library is built from [github.com/AscentsGG/hubs-encryption](https://github.com/AscentsGG/hubs-encryption) adds a signed statement here too, signed with the same key:

- `apps/mac/<version>.json` and `apps/windows/<version>.json` name the release's download and its SHA-256, the commit of hubs-encryption the library was built from, and the library's digests (code digests per architecture for the Mac, the Authenticode digest for Windows).
- The `.sig` beside each is the Ed25519 signature over its exact bytes, in base64, checked the same way as below.

The first is Hubs for Mac 0.1.69. hubs-encryption's `tools/check-mac-release.sh` rebuilds the library at the named commit and compares it with the release thehubs.gg serves.

The public key, also in `release-signing-key.pem`:

```
-----BEGIN PUBLIC KEY-----
MCowBQYDK2VwAyEAqw24MtztP9Gp2UKAhgVIjx4Y+Y0kM8IxpabNhN3K3ks=
-----END PUBLIC KEY-----
```

## Check the live site

The checker is in [github.com/AscentsGG/hubs-encryption](https://github.com/AscentsGG/hubs-encryption), which also holds the encryption code. It needs Node 20 or later and nothing else:

```bash
node tools/check-release.mjs --current
```

It reads the release thehubs.gg is running, fetches that release's statement from this repository, verifies the signature, then fetches the pages, headers and files and compares their hashes. Add `--rebuilt typescript/dist` after building the encryption files there to check that the site runs exactly that code.

You can also check a signature with OpenSSL alone:

```bash
base64 -d -i releases/<release>.json.sig > sig.bin
openssl pkeyutl -verify -pubin -inkey release-signing-key.pem -rawin -in releases/<release>.json -sigfile sig.bin
```

What a statement covers and what it does not is in [docs/release-fingerprints.md](https://github.com/AscentsGG/hubs-encryption/blob/main/docs/release-fingerprints.md). A mismatch is worth reporting to security@thehubs.gg.
