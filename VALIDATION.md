# Validation — v0.2.0

Sources: hermes-agent `origin/feat/pluggable-i18n` @ `e163ab83523` — `locales/en.yaml` (3,426 keys),
`locales/_keys.tui.json` (1,250 keys, English templates from `ui-tui` `i18n-export-en`),
`locales/_keys.desktop.json` (4,878 keys; 16 intentionally absent, see README *Not translated*).

Coverage: core 3426/3426 · TUI 1250/1250 · desktop 4862/4878. The 374 core and 9 TUI translations shipped in
v0.1.0 are byte-identical in v0.2.0 (the TUI key `overlay.close` no longer exists upstream and was dropped).

## `hermes plugins validate` (run from the `e163ab83523` worktree)

```
✓ manifest — plugin.yaml parses
✓ manifest fields — name, version, description present
✓ requires_hermes — spec '>=0.22' parses
✓ config schema — not declared
✓ requires_env — all entries UPPER_SNAKE
✓ loadable — entry: locales/ (language pack)
✓ python dependencies — none declared
✓ capability probe — skipped (no __init__.py)
✓ built-in tool collisions — no tools to check
✓ security scan — caution
✓ locale pl.desktop — pl.desktop.yaml: 4862 key(s), 4862 match the English 
desktop catalog
✓ locale pl.tui — pl.tui.yaml: 1250 key(s), 1250 match the English tui catalog
✓ locale pl — pl.yaml: 3426 key(s), 3426 match the English core catalog
⚠ no __init__.py — capability probe skipped (manifest-only plugin)
⚠ security scan caution: curl_pipe_shell (pl.desktop.yaml:1686), ssh_dir_access 
(pl.desktop.yaml:1663), ssh_dir_access (pl.desktop.yaml:1666), ssh_dir_access 
(pl.desktop.yaml:1669), ssh_dir_access (pl.desktop.yaml:1670), ssh_dir_access 
(pl.desktop.yaml:1672), ssh_dir_access (pl.desktop.yaml:1674), ssh_dir_access 
(pl.desktop.yaml:1684), sudo_usage (pl.desktop.yaml:5108), sudo_usage 
(pl.desktop.yaml:5111), sudo_usage (pl.desktop.yaml:5113), sudo_usage 
(pl.desktop.yaml:5114), sudo_usage (pl.tui.yaml:1206), sudo_usage 
(pl.tui.yaml:674), sudo_usage (pl.tui.yaml:689), sudo_usage (pl.tui.yaml:730), 
sudo_usage (pl.yaml:2319), sudo_usage (pl.yaml:2568), sudo_usage (pl.yaml:2569),
sudo_usage (pl.yaml:3960)

Validation passed.
```

The *caution* findings are translated UI strings that mention `sudo`, `~/.ssh` or `curl | sh` (the English
originals contain the same words); a language pack ships no code.

## Key-set / placeholder parity script

```
[core]    pl.yaml keys=3426 == en.yaml keys=3426; same order; placeholder_mismatch=0
[tui]     pl.tui.yaml keys=1250 == _keys.tui.json keys=1250
[desktop] pl.desktop.yaml keys=4862; _keys.desktop.json keys=4878; stray=0; intentionally absent=16 (documented in README)
RESULT: PASS
```

Per-fragment checks during translation (19 fragments): key-set equality, `{placeholder}` multiset equality,
leading/trailing whitespace and `\n` parity, matcher lists (`approval.inputs.*`) keep every English token and
append Polish ones — all `0 errors`.

## Previous release — v0.1.0

Sources: hermes-agent origin/main 98278833e29 (locales/en.yaml, apps/desktop/src/i18n/en.ts); TUI en from lane/i18n-tui worktree (10 keys).

```
[core] pl.yaml: keys=374 source=374 failures=0
[desktop] pl.desktop.yaml: keys=4862 source=4862 failures=0
[tui] pl.tui.yaml: keys=10 source=10 failures=0
[tui] _keys.tui.json parity: OK
[manifest] hermes-lang-pl 0.1.0 provides_locales: [{'id': 'pl', 'endonym': 'Polski', 'rtl': False}]
RESULT: PASS
```
