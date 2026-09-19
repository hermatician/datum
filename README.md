# kryptodillen on Hermatic — `regime-run` as a portable service

This package turns `regime-run` (the daily SMA-200 trend-regime filter
described in `regime_handoff.md`) into a Hermatic portable service, per
`hermatic-context.md`. It addresses handoff next-step #1: *"systemd unit to
keep `regime-run` (dry-run) alive across reboots."*

It does this the Hermatic way — not as a plain systemd unit on a mutable
host, but as a self-contained, Verity-protected, independently-updatable
portable service image. The host OS never changes; this service can be
updated by swapping its image.

## Why a portable service, not a plain systemd unit

`install_systemd_runner.sh` and `install_web_service.sh` (already in this
project) write directly to `/etc/systemd/system/` or `~/.config/systemd/user/`
on a normal Linux box. That model doesn't apply to Hermatic: `/usr/` is
read-only and Verity-protected, there's no package manager to pull in
dependencies at runtime, and "new long-running service" is explicitly the
portable-service layer's job per `hermatic-context.md`. A devbox would work
for development but nothing in a devbox is attested or covered by the host
trust chain — wrong fit for something trading real money.

## What changed in the Rust project

**One dependency change, no code changes:** `Cargo.toml`'s `reqwest`
dependency now specifies `default-features = false` plus `rustls-tls`,
dropping `native-tls`/OpenSSL entirely. Verified against the project's
existing `Cargo.lock` (`hyper-rustls` and `rustls 0.23.40` are already
present as transitive dependencies at a compatible version, so this is a
pure feature-flag change, not a version bump). `rusqlite`'s `bundled`
feature was already statically linking SQLite — between the two, the binary
has **zero runtime shared-library dependencies**, which is what makes a
static musl build possible.

I was not able to fully `cargo build` this end-to-end in my sandbox (no
network access to `static.rust-lang.org`/`sh.rustup.rs` to fetch a
toolchain new enough for the current dependency graph) — the diff is small
and the dependency-resolution math checks out against your real lockfile,
but **run `cargo build --release` yourself before trusting this in
deployment**, the same way you'd review any dependency change.

```diff
--- a/Cargo.toml
+++ b/Cargo.toml
@@ -5,7 +5,9 @@
 
 [dependencies]
 # HTTP
-reqwest = { version = "0.12", features = ["blocking", "json"] }
+reqwest = { version = "0.12", default-features = false, features = ["blocking", "json", "rustls-tls"] }
```

No `src/` changes — TLS backend selection is purely a Cargo feature, and
nothing in `client.rs` references `native_tls` or `rustls` types directly.

## Files in this package

```
Cargo.toml                            # patched (rustls instead of native-tls)
mkosi/
  mkosi.conf                          # portable service image definition
  kryptodillen-regime.service         # the systemd unit, baked into the image
scripts/
  build.sh                            # static musl build + mkosi image build
  seal_credentials.sh                 # systemd-creds TPM2 sealing (run on host)
  deploy.sh                           # portablectl attach + enable (run on host)
  nftables-kryptodillen.conf          # egress allow-list (Binance API only)
```

## End-to-end flow

**1. Build (inside a `hermatic devbox`):**
```bash
hermatic devbox create build
hermatic devbox enter build
# inside the devbox:
sudo pacman -S musl mkosi rustup
./scripts/build.sh
```
This cross-compiles `kryptodillen` for `x86_64-unknown-linux-musl` with
static linking (`-C target-feature=+crt-static`), stages it plus the unit
file into `mkosi.extra/`, and runs `mkosi build` to produce
`mkosi/mkosi.output/kryptodillen-regime.raw` — a GPT image with its own
`/usr/`, a Verity partition, and a PKCS#7 signature, structurally identical
to the Hermatic OS image itself.

**2. Sign the image** with this host's portable-service signing key — wired
into `mkosi.conf`'s `[Validation]` section; point
`VerityKeyAndCertificate=` at your actual key/cert pair before building
(placeholder path included, **do not** commit a real key into version
control).

**3. Copy the `.raw` image to the target host** (the devbox's `$HOME` bind
mount is the easiest path out).

**4. Seal credentials, on the target host, as root:**
```bash
sudo ./scripts/seal_credentials.sh
```
Prompts interactively for the Binance API key/secret (never passed as
arguments, never written to disk in plaintext) and seals them with
`systemd-creds encrypt --tpm2-pcrs=...`. This matches `config.rs::load_keys`,
which already reads from `CREDENTIALS_DIRECTORY` — that code needed **no
changes** for this to work; it was already written for the systemd-creds
convention.

**5. Deploy, on the target host, as root:**
```bash
sudo ./scripts/deploy.sh /path/to/kryptodillen-regime.raw
```
Runs `portablectl attach --now --enable`, which registers the image and
starts `kryptodillen-regime.service`.

**6. Restrict egress (optional but recommended):** install
`scripts/nftables-kryptodillen.conf` per its header comment, so the service
can reach Binance's API and DNS only — nothing else outbound.

## Operational notes specific to this packaging

- **Dry-run by default.** The shipped unit's `ExecStart=` does **not**
  include `--live`, matching the handoff's design decision #5 and "Don't"
  section. Flip to live only via a `systemctl edit` drop-in after the
  multi-week dry-run, never by hand-editing a running image.
- **State survives image updates.** The SQLite DB lives in
  `/var/lib/kryptodillen` (via `StateDirectory=` / `DynamicUser=yes`), which
  is separate from the image's own `/usr/`. Swapping the image
  (`portablectl reattach`) for a code update does not touch `regime_state`
  or kline history.
- **`DynamicUser=yes`** means no fixed UID/GID to provision — systemd
  allocates one at start and owns `/var/lib/kryptodillen`'s permissions.
  This is why `scripts/deploy.sh`'s `mkdir` is just a safety net, not the
  real permission-setting step.
- **Updating the service** later: rerun `scripts/build.sh`, sign, copy over,
  then `portablectl reattach --now kryptodillen-regime /path/to/new.raw` —
  no host OS change, per Hermatic's update model.
- **Monitoring** still uses the same commands from the handoff —
  `journalctl -u kryptodillen-regime.service -f` and the `regime_state`
  SQL query — `deploy.sh` prints both at the end of a successful attach.

## What I deliberately left for you to fill in / verify

- The real Verity signing key path in `mkosi.conf`.
- The PCR bank set in `seal_credentials.sh` (`7+11+14` is a reasonable
  default but confirm against `systemd-analyze pcrs` on the actual host —
  PCR usage is build-specific).
- A real `Documentation=` URL in the unit file.
- The Binance IP-refresh mechanism in the nftables config is sketched, not
  wired to a timer — adapt to whatever pattern this host already uses for
  similar allow-lists, and verify against Binance's current API edge
  behavior before relying on it as a hard gate before going live.
- Running `cargo build --release` yourself to confirm the rustls switch
  compiles clean in your actual environment, per the caveat above.

This doesn't touch the strategy logic, the regime signal, or any of the
"locked" decisions in the handoff — it's packaging only, per the handoff's
explicit instruction not to relitigate the strategy itself.
