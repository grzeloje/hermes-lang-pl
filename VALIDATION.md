# Validation — desktop 4992 keys

Sources: hermes-agent `origin/main` @ `a036b13793a` — `apps/desktop/src/i18n/en.ts`
(4933 keys) cross-checked against `locales/_keys.desktop.json`.

Coverage: desktop 4992 / 4916 English keys. 127 strings added: 104 for keys added to the
English catalog since v0.2.0, plus 23 more from the 2026-10-06 catalog growth (Settings ▸ Plugins,
model-menu limit rows, status-bar backend/messaging health), and six existing entries corrected — they carried
placeholders the English entry does not have, so `adaptStringOverrides` never resolved
them and the UI fell back to English. 17 entries remain intentionally absent (see README
*Not translated*); 76 keys in the file are not in the English catalog (residue from
removed features, ignored at runtime — carried over unchanged).

## `hermes plugins validate`

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
✓ locale pl.desktop — pl.desktop.yaml: 4992 key(s), 4916 match the English
  desktop catalog
✓ locale pl.tui — pl.tui.yaml: 1250 key(s), 1241 match the English tui catalog
✓ locale pl — pl.yaml: 3426 key(s), 3422 match the English core catalog
⚠ no __init__.py — capability probe skipped (manifest-only plugin)
⚠ pl.desktop.yaml: 76 key(s) not in the English desktop catalog (ignored at runtime)
⚠ pl.tui.yaml: 9 key(s) not in the English tui catalog (ignored at runtime)
⚠ pl.yaml: 4 key(s) not in the English core catalog (ignored at runtime)

Validation passed.
```

The *not in catalog* warnings are pre-existing: those keys date from v0.2.0 and earlier and
are not touched here.

## Key-set / placeholder parity script

```
[desktop] pl.desktop.yaml keys=4992; en.ts keys=4933; covered=4916; added=127
          placeholder_mismatch=0   symbol_mismatch=0   non_text_leaves=0
          removed_from_previous=0  values_changed=6
RESULT: PASS
```

Per-key checks during translation and merge: key-set equality, `{placeholder}` multiset
equality, emoji/symbol preservation, and a YAML round-trip through Hermes' own
`i18n_layers.parse_locale_file` (this catches a value that YAML re-reads as a non-string —
an unquoted `0` parses as a number and `flatten` silently drops it).

Diff shape: removing the 104 added keys and restoring the six corrected values
reproduces the v0.2.0 file byte for byte.

## Previous release — v0.2.0

Sources: hermes-agent `origin/feat/pluggable-i18n` @ `e163ab83523` — `locales/en.yaml` (3,426 keys),
`locales/_keys.tui.json` (1,250 keys, English templates from `ui-tui` `i18n-export-en`),
`locales/_keys.desktop.json` (4,878 keys; 16 intentionally absent, see README *Not translated*).

Coverage: core 3426/3426 · TUI 1250/1250 · desktop 4862/4878. The 374 core and 9 TUI translations shipped in
v0.1.0 are byte-identical in v0.2.0 (the TUI key `overlay.close` no longer exists upstream and was dropped).

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
