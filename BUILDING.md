# Building Cyrus

Notes for building Cyrus from source. Tracks the upstream `tailcallhq/forgecode` build but pins the prerequisites we've actually verified.

## Toolchain

Rust toolchain is pinned by `rust-toolchain.toml` — currently **`1.92.0`**. With `rustup` installed, the correct toolchain auto-fetches on first `cargo` invocation.

If you don't have `rustup`:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --default-toolchain stable
```

The pinned 1.92.0 will install on top of stable when you run `cargo` inside the repo.

## System dependencies

`forge_repo` uses `prost-build` for protobuf code generation, which requires the `protoc` compiler at build time. Without it, `cargo build` fails partway through with `Could not find protoc`.

| Platform | Install |
|---|---|
| **macOS (Homebrew)** | `brew install protobuf` (also pulls `abseil`) |
| **Debian / Ubuntu** | `sudo apt install protobuf-compiler libprotobuf-dev` |
| **Fedora / RHEL** | `sudo dnf install protobuf-compiler protobuf-devel` |
| **Arch** | `sudo pacman -S protobuf` |
| **Nix** | `nix develop` — `flake.nix` in this repo provides a dev shell |

Verify after install: `protoc --version` (Cyrus has been verified against `libprotoc 34.1`).

No other system dependencies have surfaced during `cargo build --release`. If you hit one, update this table and open a ticket.

## First build

```bash
cd ~/repos/cyrus
cargo build --release
```

Cold build on Apple Silicon (M-series): ~10-15 minutes. Rebuilds with the incremental cache are much faster (4-5 min for a touched-everything rebuild, seconds for typical edit-rebuild cycles).

The release profile uses `lto = true`, `codegen-units = 1`, `opt-level = 3`, and `strip = true` — that's why the cold build is slow but the resulting binary is small (~33MB).

## Verifying

```bash
./target/release/forge --version
```

Expected: `forge 0.1.0-dev` on `main` (the published release tag will differ; `0.1.0-dev` is the workspace version in `Cargo.toml`).

## Git hooks

There are no git hooks. See [FORK.md § Git hooks](./FORK.md#git-hooks) for the rationale.

## Tests

Out of scope for this doc. See upstream `forgecode` test docs and `cargo test --workspace`.
