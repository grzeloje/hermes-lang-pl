# hermes-lang-pl — Polish language pack for Hermes Agent

**Polski** dla Hermes Agent: komunikaty CLI i gatewaya (rdzeń), aplikacja Hermes Desktop oraz TUI.

This is the reference *language pack* plugin for [Hermes Agent](https://github.com/NousResearch/hermes-agent):
a **text-only** plugin — no Python, no JavaScript, no network. It ships three YAML catalogs that Hermes layers
over its bundled English:

| File | Surface | Source it mirrors | Keys |
|---|---|---|---|
| `locales/pl.yaml` | core (Python `t()`: approval prompts, gateway/slash replies, CLI, tips, tool verbs…) | `locales/en.yaml` | 3426 / 3426 |
| `locales/pl.desktop.yaml` | Hermes Desktop | `apps/desktop/src/i18n/en.ts` (`locales/_keys.desktop.json`) | 4862 / 4878 (16 skipped, see below) |
| `locales/pl.tui.yaml` | Hermes TUI (`hermes --tui`) | `ui-tui/src/i18n/en.ts` (`locales/_keys.tui.json`) | 1250 / 1250 |

## Install

```bash
hermes plugins install https://github.com/teknium1/hermes-lang-pl
hermes config set display.language pl
```

Restart the gateway / Desktop / TUI afterwards. `hermes plugins validate <path>` on a checkout of this repo
confirms the pack parses and its keys are known. Requires Hermes `>= 0.22` (pluggable i18n).

To go back: `hermes config set display.language en`; to remove: `hermes plugins uninstall hermes-lang-pl`.

## How it works

- `plugin.yaml` declares `provides_locales: [{id: pl, endonym: Polski, rtl: false}]`. The plugin loader
  registers every `locales/pl[.tui|.desktop].yaml` automatically (`ctx.register_locale_dir`) — a manifest-only
  pack needs no `__init__.py`.
- Resolution order for a key: plugin pack → user overlay (`$HERMES_HOME/locales/pl.yaml`) → bundled → English.
  Packs may be partial; missing keys fall back to English, so this pack keeps working when Hermes adds strings.
- Registering the pack never changes `display.language`; you opt in with the config command above.

## Pack conventions (apply to any language pack)

- **Keys** are copied verbatim from the English catalogs and nested the same way (leaf keys that contain dots,
  e.g. `fieldLabels."display.personality"`, stay a single quoted key). YAML reserved words used as keys
  (`yes`, `no`, `on`, `off`) are quoted.
- **Core placeholders** are named and must match English exactly: `{count}`, `{error}`, `{model}` … (`str.format`).
- **Desktop/TUI function-valued entries** — English catalogs contain entries like
  `(name, count) => \`Install ${name} (${count})\``. A pack expresses those as a plain string with **positional
  placeholders in argument order**: `"Zainstaluj {0} ({1})"`. The renderer wraps the string back into a function.
  Only arguments the English text actually interpolates need to appear; arguments used purely as
  booleans/discriminators are dropped (see *Not translated* below).
- **Plural forms**: positional strings cannot branch on the count, so Polish uses number-neutral phrasing
  (`Narzędzia: {0}`, `{0} wyników`) instead of the English `tool${n === 1 ? "" : "s"}` ternary.
- **Left in English on purpose**: product names (Hermes, Nous, Skills Hub, Kanban), slash-command and tool names,
  config keys (`display.language`), paths, model/provider ids, code in backticks, keyboard key names.
- Hotkey prompts keep the English letters (`[o] jednorazowo | [s] na sesję | [a] zawsze | [d] odrzuć`,
  `Wybór [o/s/a/D]:`) because the letters are the accepted inputs.

### Not translated (fall back to English)

16 Desktop entries cannot be expressed as a positional string and are intentionally absent from
`pl.desktop.yaml` (the app shows English for them): editorial intro content (`intro.custom`), array/object leaves
(`composer.newSessionPlaceholders`, `composer.followUpPlaceholders`, `sidebar.projects.branchOff`), identity
pass-throughs (`settings.vault.identifierShown`, `commandCenter.maintenance.bytes`, `rightSidebar.folderTip`) and
boolean-toggle labels (`skills.toggleToolset`, `settings.model.moaReferenceToggle`, `commandCenter.pets.toggleFailed`,
`webhooks.toggleFailed`, `sidebar.projects.toggle`, `desktop.yoloSystem`, `ui.sidebar.toggle`,
`settings.plugins.installModal.skillsReady`, `statusStack.control.gateLastExit`).

## Validation

`hermes plugins validate <checkout>` (Hermes `feat/pluggable-i18n`) reports every locale file against the English
catalog it mirrors — see [VALIDATION.md](VALIDATION.md) for the recorded output of the current release.
The pack is text-only, so the plugin security scanner's *caution* findings (`sudo`, `~/.ssh`, `curl | sh`
mentioned inside translated UI strings) are expected and are not code.


Every release is additionally checked with a script that asserts, per surface: YAML parses; every leaf is a string; the flattened
key set equals the English key set (minus the documented skips); and the placeholder set of every value equals
the English one. The check lives in the Hermes i18n campaign tooling (`check_pack.py`); its output for this release
is recorded in the release notes.

## Contributing

Fix a string → edit the YAML, keep the key and placeholders, open a PR. New Hermes strings → add the key under the
same path as English; unknown keys are reported by `hermes plugins validate` as warnings, never errors.

## License

MIT — see `LICENSE`.
