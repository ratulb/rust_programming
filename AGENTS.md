# AGENTS.md

## Structure

- **~120 independent Cargo crates** under root — one per algorithm/problem. Each is a `lib.rs` crate with `edition = "2021"` (except `doc_gen/` which is a binary, `edition = "2024"`).
- **No workspace or root `Cargo.toml`**. You must `cd` into a crate directory to build/test it. `cargo test` at root will fail.
- **No external dependencies** — all crates are std-only.
- **mdBook** generates docs from `src/` (the mdBook source directory, not Rust code). Output lands in `docs/`. `book.toml` configures it.

## Commands

| Action | Command |
|---|---|
| Test one crate | `cd <crate_dir> && cargo test` |
| Run all crate tests | `for d in */; do (cd "$d" && cargo test 2>&1); done` |
| Build docs | `mdbook build` (output: `docs/`) |
| Generate doc scaffolding | `cd doc_gen && cargo run -- <crate_dir> <src_dir>` |

## CI

- `.github/workflows/mdbook.yml` only builds + deploys mdBook to GitHub Pages. **It does not run `cargo test`**.
- Runs on push/PR to `main`.

## Key Files

| Path | Purpose |
|---|---|
| `book.toml` | mdBook config (src=`src/`, build-dir=`docs/`) |
| `src/SUMMARY.md` | mdBook chapter index |
| `theme/custom.css` | mdBook theme overrides |
| `.gitignore` | Ignores `**/target/`, `**/Cargo.lock`, `*.rs.bk` |
| `README-installation-notes.md` | First-time Rust setup guide |

## Style / Conventions

- Each `lib.rs` has a public function and a `#[cfg(test)] mod tests` block at the bottom.
- No rustfmt, clippy, or lint config files — uses Rust defaults.
- No lockfiles tracked in git.

## `doc_gen` Tool

`doc_gen/src/main.rs` reads a crate's `lib.rs`, extracts the first `# Title` line and `## description` lines, writes an `introduction.md` into `src/<crate_name>/`, and appends a link to `src/SUMMARY.md`. Useful for bootstrapping mdBook chapters from existing crates.
