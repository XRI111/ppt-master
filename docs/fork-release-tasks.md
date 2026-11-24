# Fork release tasks (XRI111/ppt-master)

Tasks to land in the next release of this fork. The installed plugin (`~/.claude/plugins/cache/ppt-master/…`) is
overwritten on plugin update, so a local-only change is lost until it is committed and pushed here.

## 1. Confirm UI: never time out, add Cancel — implemented, not yet committed

- Server idle timeout defaults to `0` (never). The page stays live until it is submitted, cancelled, or `--shutdown`.
- A **Cancel** button on the page calls `POST /api/cancel`, which writes `confirm_ui/cancelled.json` and stops the server.
  Waits return `130` and never recover a cancelled page. `--daemon` clears the receipt on a fresh launch.
- Docs: a wait timeout (`124`) means re-attach and keep waiting, not fall back to chat. These are in `scripts/docs/confirm_ui.md`,
  `workflows/governance/failure-recovery.md` and `references/strategist-template.md`.
- Files: `scripts/confirm_ui/server.py`, `static/app.js`, `static/index.html`, `static/style.css` plus the docs above.
- Release steps: commit, push, update the plugin, then confirm the installed `static/index.html` contains `btn-cancel`.

## 2. Translate the entire system to English — not started

Requested 2026-11-24. The upstream project is Chinese-first, and Chinese leaks into the English workflow (for example, the
Stage-1 **Deck** dropdown only offers `中国电信` and `中汽研`). As of that date, 536 tracked files contain Chinese text:

| Area | Files | Notes |
|---|---:|---|
| `examples/` | 441 | Example projects; translate, replace with English examples, or drop from the fork |
| `skills/ppt-master/scripts/` | 30 | Comments, log/CLI strings, confirm UI catalogs and i18n |
| `skills/ppt-master/templates/` | 21 | Template IDs and folder names (`中国电信`, `中国电建`, `中汽研`), `*_index.json` summaries, design specs |
| `docs/` | 15 | Includes `docs/zh/` |
| `skills/ppt-master/references/` + `workflows/` | 18 | Prompt/reference prose; follow `docs/rules/prompt-style.md` |
| Root | ~7 | `README_CN.md`, `SPONSORING_CN.md`, `.env.example`, `requirements.txt`, `.github/` |

Decisions to make first:
- Keep the confirm page's multi-language switcher (en/ja/zh/zh-TW), or ship English-only?
- Renaming Chinese template IDs changes the keys in `*_index.json` and on-disk folder names. Should old IDs get aliases?
- `docs/zh/` and `*_CN.md`: translate and delete, or keep as secondary docs?
- Upstream sync: a large translation diff makes merging from `hugohe3/ppt-master` harder. Pick a strategy (translate once
  and stop syncing, or keep translations in separate files) before starting.

Suggested approach: run it as its own feature (brainstorm, then plan), and work area by area with English IDs and aliases.
