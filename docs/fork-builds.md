# Fork cloud builds

The upstream workflows are adapted for this fork using supported checkout,
Go setup, and artifact actions. Builds keep Go 1.18.10 to match the existing
dependencies. They do not use the legacy `.goreleaser.yml`, which targets
upstream registries and releases.

## Build and download

- Push a branch or open a pull request to run tests and build CLI packages.
- Alternatively, open **Actions > ci > Run workflow** and select the branch.
- Select `dev-jinbo` for the existing internal router image customization.
- Download packages from **Artifacts** on the completed workflow run.
- Linux supports i386, x86_64, and arm_64; macOS supports x86_64 and arm_64;
  Windows supports i386, x86_64, and arm_64.
- Windows ZIPs include `ktctl.exe`, the matching `wintun.dll`, and its license.
  Extract them together and run `ktctl.exe` in an elevated Windows terminal.
  The Windows executable can also be launched from an elevated Cygwin shell.
- Each package includes a separate SHA-256 checksum file. Artifacts expire
  after 30 days.

Branch builds use CLI version `0.3.7` so that default helper image tags remain
compatible with upstream v0.3.7. The archive filename also includes the commit
SHA. Existing image constants and command-line overrides are unchanged.
For example, an internal shadow image can still be selected with:

```powershell
.\ktctl.exe connect --image hub.imgo.tv/argon/rdc-incubator/kt-connect-shadow:v0.3.7
```

For mesh, use `--routerImage` if the internal router image tag does not match
the CLI version.

## Publish a release

Push a version tag such as `v0.3.7-imgo.1` pointing to the desired branch commit.
The release workflow builds the same packages and publishes them to this fork's
GitHub Releases using `GITHUB_TOKEN`. Tag builds embed the tag without its
leading `v` as the CLI version; explicitly override helper image tags if needed.

No Docker images are built or pushed, no upstream Homebrew formula is changed,
and no registry credentials or extra secrets are needed. These workflows do
not automatically merge future upstream changes.
