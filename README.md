# HttpsAgent

Automatic HTTPS certificates for Windows, macOS and Linux.

> **Preview.** The current release only shows version information. Certificate management is not available yet.

## Install

### macOS and Linux (Homebrew)

```sh
brew tap httpsagent/tap
brew install httpsagent
```

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

## Usage

```
httpsagent              Show information
httpsagent --version    Show the version
```

## License

HttpsAgent is free to use. Its source code is not published. See [LICENSE](LICENSE).
