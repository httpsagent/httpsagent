# HttpsAgent

The TLS certificates of your web servers — IIS, nginx, Apache, LiteSpeed, Caddy — and when they expire. Windows, macOS
and Linux.

`httpsagent list` shows the TLS (HTTPS) certificates on a machine, grouped by the web server that uses them, with their
expiry dates and the program that obtained them (certbot, acme.sh, win-acme, Certify The Web, hosting panels). It also
lists the web servers it cannot read yet, and development certificates for `localhost`. As a table for people, or as
JSON for scripts and monitoring. It only reads: no network, no administrator rights needed.

Certificate lifetimes are getting shorter (200 days today, 47 days by 2029) and Let's Encrypt no longer emails expiry
warnings, so it pays to see your expiry dates yourself.

> **Preview.** Automatic renewal is not available yet.

## Install

### Quick install (Linux and macOS)

```sh
curl -fsSL https://pkg.httpsagent.com/install.sh | sh
```

[install.sh](https://pkg.httpsagent.com/install.sh) uses the apt or dnf repository below where it can, Homebrew on
macOS, and otherwise the release archive (checked against `SHA256SUMS`) into `/usr/local/bin`, or `~/.local/bin`
without root. `HTTPSAGENT_VERSION=0.2.3` installs a given version.

### macOS and Linux (Homebrew)

```sh
brew install httpsagent/tap/httpsagent
```

Update with `brew upgrade httpsagent`.

### Ubuntu and Debian (apt)

Ubuntu 22.04, Debian 12 and later, x64 and ARM64:

```sh
sudo curl -fsSLo /etc/apt/sources.list.d/httpsagent.sources https://pkg.httpsagent.com/apt/httpsagent.sources
sudo apt update && sudo apt install httpsagent
```

Updates come with `sudo apt update && sudo apt upgrade`. The file adds the repository together with its signing key,
`HttpsAgent Release Signing <release@httpsagent.com>`, fingerprint `2F3F B8E9 9F99 6554 80C8 0044 51F1 8BF9 E034 6FBF`
(the key alone: <https://pkg.httpsagent.com/apt/gpg.key>).

### RHEL, Rocky, Alma, Fedora and Amazon Linux (dnf)

RHEL 9, Amazon Linux 2023, Fedora and later, and their rebuilds; x64 and ARM64:

```sh
sudo curl -fsSLo /etc/yum.repos.d/httpsagent.repo https://pkg.httpsagent.com/rpm/httpsagent.repo
sudo dnf install httpsagent
```

Updates come with `sudo dnf upgrade`. Packages and package lists are signed with
`HttpsAgent RPM Signing <release@httpsagent.com>`, fingerprint `4B00 FFB7 7427 F420 8430 BFDB F770 7E48 552B 4AE7`
(<https://pkg.httpsagent.com/rpm/gpg.key>); dnf asks once to import it.

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
| Debian / Ubuntu x64 | `httpsagent_<version>_amd64.deb` |
| Debian / Ubuntu ARM64 | `httpsagent_<version>_arm64.deb` |
| RHEL / Fedora / Amazon Linux x64 | `httpsagent-<version>-1.x86_64.rpm` |
| RHEL / Fedora / Amazon Linux ARM64 | `httpsagent-<version>-1.aarch64.rpm` |

- Every release includes `SHA256SUMS` to verify the downloads.
- Linux builds need glibc 2.34 or later (Ubuntu 22.04, Debian 12, RHEL 9, Amazon Linux 2023 and later).
- On macOS, prefer Homebrew: macOS blocks files downloaded with a web browser because the binary is not notarized.

### Antivirus warnings

HttpsAgent releases are not code-signed yet. Some antivirus products (for example Norton's Download Intelligence) hold a new, unsigned release on its first run and scan it in the cloud, which takes about a minute; `httpsagent` starts normally once the scan finishes. On Windows, SmartScreen may also warn when an archive downloaded with a browser is opened from Explorer. If in doubt, compare your download with `SHA256SUMS`.

## Usage

```
httpsagent list           TLS certificates, grouped by the web server that uses them
httpsagent list --all     Also every other certificate on this machine (CAs, code signing, ...)
httpsagent list --json    The same as JSON for scripts and monitoring (works with --all too)
httpsagent --version      Show the version
httpsagent                Show information
```

**What `list` shows.** TLS server certificates: the kind a web server (and later a mail server) uses, public ones from
Let's Encrypt, Sectigo, DigiCert and others, and development certificates for `localhost` (marked `(dev)`). They are
grouped under the web server that uses them — IIS, IIS Express and other http.sys services on Windows; nginx, Apache,
LiteSpeed and Caddy on Linux; Apache, and nginx, httpd and Caddy from Homebrew on macOS — with the binding (address,
port, host name, IIS site or configuration file). Certificates no web server uses come first. Every row says when the
certificate expires (expired ones in red, fewer than 30 days left in yellow, the ones that end soonest first) and, where
the evidence is on the machine, which program obtained it. A web server that refers to a certificate that is not there
shows a red "missing certificate" row.

Web servers and hosting software whose certificates HttpsAgent cannot read yet — HAProxy, OpenLiteSpeed, Traefik,
Lighttpd, Tomcat, ASP.NET Core (Kestrel), Apache and nginx on Windows (XAMPP, WampServer, Laragon), cPanel, Plesk,
DirectAdmin and other panels, Docker — are listed under "certificate support in development".

**`--all`** adds every other certificate on the machine — certificate authorities (marked `system` when they came with
the operating system), client, code signing and email certificates — in a section of their own: HttpsAgent shows them,
but they are not TLS server certificates and it does not manage them.

**`--json`** writes the same as one JSON document: every certificate with its `category` (`server`, `development`,
`ca`, …), `inScope`, `obtainedBy` and the `servers` that use it; every detected server with its `support` level
(`full` or `planned`), the `evidence` that it is installed and its `bindings` (each with a `status`: `found`, `missing`,
`unreadable`, …); `hidden` counts what was left out without `--all`. Field names are a contract: later versions add
fields, they do not rename them. For example, the certificates IIS uses that expire within 30 days:

```sh
httpsagent list --json | jq '.certificates[] | select(.servers | index("iis")) | select(.daysLeft < 30) | .name'
```

**Permissions.** It needs no administrator rights. Places it cannot read are listed below the table; running with
`sudo` or as Administrator includes them (for example Let's Encrypt's private directory on Linux, or IIS site names on
Windows). It reads only — it changes nothing, never decodes, shows or stores private keys (only whether one is
present), and makes no network connection.

## License

HttpsAgent is free to use. Its source code is not published. See [LICENSE](LICENSE).
