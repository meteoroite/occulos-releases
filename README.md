# OcculOS — public update channel

This repository is the **public distribution channel** for OcculOS. It exists so
that the in-app updater can reach the manifest and the installer without any
GitHub credentials — a field install has no token, so anything hosted behind a
private repository returns HTTP 404 to every customer.

It contains only:

- `update.json` — the signed update manifest the in-app updater fetches.
  Served at:
  `https://raw.githubusercontent.com/meteoroite/occulos-releases/main/update.json`
- GitHub **Releases** — the installer asset(s) the manifest points at
  (`https://github.com/meteoroite/occulos-releases/releases/download/vX.Y.Z/OcculOS-setup-X.Y.Z.exe`).

The application source lives in a separate **private** repository. Only the
release artifacts are public here.

## Do not hand-edit `update.json`

It is signed with the release authority key. A manifest that fails signature
verification is refused fail-closed by every install, and an unsigned or
hand-edited manifest silently withholds the update from everyone. Produce it with
the release pipeline, or re-sign it with:

```
dotnet run --project tools/OcculOS.KeyTool -- --sign-update update.json \
    --installer dist/OcculOS-setup-X.Y.Z.exe
```

Both signatures are published deliberately: `signatureV2` covers the entitlement
fields (`releaseDate`, `minimumLicenseVersion`, `requiredEntitlement`) and is
required by 1.0.6+ builds; `signature` (V1) is kept so installs already running in
shops keep updating through the transition.
