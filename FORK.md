# Cyrus — Fork of ForgeCode

## What this is

**Cyrus** is Bearsoft's agentic-pipeline harness, forked from [tailcallhq/forgecode](https://github.com/tailcallhq/forgecode) (Apache-2.0).

- **Fork:** [geekjimbo/cyrus](https://github.com/geekjimbo/cyrus)
- **Upstream:** [tailcallhq/forgecode](https://github.com/tailcallhq/forgecode) (formerly `antinomyhq/forge`; GitHub redirects the old URL)
- **License:** Apache-2.0 (preserved unchanged from upstream)
- **Linear project:** [cyrus](https://linear.app/thebearsoft/project/cyrus-8d538a0fa893)

## Why we forked

The previous Bearsoft agentic pipeline (Python `cyrus`/`ap-init` CLIs, Mem0, LiteLLM, Langfuse, custom `.cyrus-bridge/` JSON protocol, `~/.cyrus/` install tree) was [purged on 2026-04-27](file:~/cyrus-purge-report-20260427-110324.md). The pivot: a Rust-based harness gives us better startup latency, smaller distribution, and a single statically-linked binary — and Forge already implements most of the runtime we'd otherwise build from scratch.

Forking instead of using upstream directly:

- We need defaults and integrations that aren't in scope for upstream (Linear-as-bus, Bearsoft skill set, opencode config templates, sovereign-AI provider routing).
- We want freedom to ship internal-only changes without waiting on upstream review.
- Generic fixes should still flow back upstream — we are good citizens, not orphan-fork hermits.

## Upstream sync policy

- **Cadence:** pull from `upstream/main` weekly (or when a release tag drops), whichever comes first.
- **Mechanism:** `git fetch upstream && git merge upstream/main` on a `sync/upstream-YYYY-MM-DD` branch, PR into `main`.
- **Conflict policy:** prefer upstream behavior unless a Bearsoft-specific divergence has a Linear ticket explaining why.
- **Tag tracking:** mirror upstream release tags as `upstream-vX.Y.Z` so we can diff against any historical baseline.

## What we modify vs preserve

| Area | Policy |
|---|---|
| Core Forge runtime (event loop, agent state machine, tool dispatch) | **Preserve** — modify only via upstream PR. |
| Model providers (Anthropic, OpenAI, Bedrock, Vertex, etc.) | **Preserve** — generic fixes go upstream; Bearsoft-only providers (e.g., LiteLLM) live in plugins. |
| Tool schemas | **Preserve** — adding tools is fine; modifying upstream tools requires upstream PR. |
| Default configuration / branding | **Modify** — Cyrus identity, default model selection, opencode template references. |
| Linear/agent-pipeline integrations | **Add** — net-new code under `crates/cyrus-*` (or whatever crate naming we settle on). |
| `LICENSE`, `NOTICE`, `Cargo.toml` license fields | **Preserve** — Apache-2.0 attribution unchanged. |

## Contribution flow

1. **Generic fix or feature** — open a PR against `tailcallhq/forgecode` first. If accepted upstream, sync down. If declined, apply locally only with a Linear ticket noting why.
2. **Bearsoft-specific feature** — open a Linear ticket in the [cyrus project](https://linear.app/thebearsoft/project/cyrus-8d538a0fa893), branch from `main` as `feat/cra-NNN-...`, PR into `main`.
3. **Sync from upstream** — `sync/upstream-YYYY-MM-DD` branch, automated where possible.

## Git hooks

**No git hooks.** Cyrus follows upstream `tailcallhq/forgecode` here — no `.husky/`, no `lefthook.yml`, no `.pre-commit-config.yaml`, no `prepare` script. Quality gates (formatting, linting, tests) run in CI only.

The old Bearsoft pipeline (pre-2026-04-27) used `~/.githooks/` for commit-msg, pre-commit, pre-push, and a worktree manager. Those hooks were tied to the deleted Python `ap-init`/`cyrus` CLIs and have been archived under `Projects/cyrus-old-archive/githooks-backup/` in the Obsidian vault for reference.

If we later decide we want hooks, we'll write fresh ones against the Rust workflow (`cargo fmt`, `cargo clippy`, `cargo test`) rather than resurrect the Python-coupled archive.

## Apache-2.0 compliance

- `LICENSE` preserved verbatim.
- Any `NOTICE` file from upstream is preserved.
- Modifications are documented in commit history; substantive changes also noted in `CHANGELOG.md` (when introduced).
- Redistributions of this fork retain the Apache-2.0 LICENSE and copyright notice per Section 4 of the license.

## See also

- [`BUILDING.md`](./BUILDING.md) — toolchain, system dependencies, build verification.
- [`README.md`](./README.md) — Forge's full README (Cyrus banner prepended).
- [Linear: cyrus project](https://linear.app/thebearsoft/project/cyrus-8d538a0fa893) — task tracking.
- [`~/cyrus-purge-report-20260427-110324.md`](file:~/cyrus-purge-report-20260427-110324.md) — what the previous Cyrus stack was, and why it was retired.
