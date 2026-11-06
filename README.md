# HttpsAgent

Automatic HTTPS certificates for Windows, macOS and Linux.

> **Preview.** `httpsagent list` shows the certificates installed on a machine and when they expire. Certificate renewal is not available yet.

## Install

### macOS and Linux (Homebrew)

```sh
brew install httpsagent/tap/httpsagent
```

Update with `brew upgrade httpsagent`.

### Windows (Scoop)

```powershell
scoop bucket add httpsagent https://github.com/httpsagent/scoop-bucket
scoop install httpsagent
```

Update with `scoop update httpsagent`.

### Download

Download the archive for your platform from [Releases](https://github.com/httpsagent/httpsagent/releases), extract it and run `httpsagent`.

| Platform | File |
|---|---|
| Windows x64 | `httpsagent-<version>-win-x64.zip` |
| Windows ARM64 | `httpsagent-<version>-win-arm64.zip` |
| macOS Apple Silicon | `httpsagent-<version>-osx-arm64.tar.gz` |
| macOS Intel | `httpsagent-<version>-osx-x64.tar.gz` |
| Linux x64 | `httpsagent-<version>-linux-x64.tar.gz` |
| Linux ARM64 | `httpsagent-<version>-linux-arm64.tar.gz` |

- Every release includes `SHA256SUMS` to verify the downloads.
- Linux builds need glibc 2.35 or later (Ubuntu 22.04, Debian 12 and later).
- On macOS, prefer Homebrew: macOS blocks files downloaded with a web browser because the binary is not notarized.

### Antivirus warnings

HttpsAgent releases are not code-signed yet. Some antivirus products (for example Norton's Download Intelligence) hold a new, unsigned release on its first run and scan it in the cloud, which takes about a minute; `httpsagent` starts normally once the scan finishes. On Windows, SmartScreen may also warn when an archive downloaded with a browser is opened from Explorer. If in doubt, compare your download with `SHA256SUMS`.

## Usage

```
httpsagent list           List the certificates installed on this machine
httpsagent list --all     Also list the ones that came with the operating system
httpsagent list --json    The same as JSON, for scripts (works with --all too)
httpsagent --version      Show the version
httpsagent                Show information
```

`list` reads the certificate stores (Windows), the keychains (macOS) or the CA directories and web server
configuration — Let's Encrypt, nginx, Apache — (Linux). Certificates that came with the operating system are marked
`system`, everything else `installed`; when in doubt a certificate is shown as `installed`. By default only the
installed ones are listed and the system ones are counted below the table; `--all` lists them too. It needs no administrator
rights: places it cannot read are listed below the table (run with `sudo` or as Administrator to include them). It
reads only — it changes nothing, never decodes, shows or stores private keys (only whether one is present), and makes
no network connection.

## License

HttpsAgent is free to use. Its source code is not published. See [LICENSE](LICENSE).
