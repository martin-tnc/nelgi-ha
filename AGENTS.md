# Repository Guidelines

## Project Structure & Module Organization
- Home Assistant config lives at repo root, mirroring `/config` on the appliance. Key files: `configuration.yaml`, `automations.yaml`, `scripts.yaml`, `scenes.yaml`, `blueprints/`, `custom_components/`, `www/`, `templates/`, `automations/`.
- Excluded runtime/state (DBs, logs, `.storage/`, `tts/`) per `.gitignore`.
- Keep custom integrations in `custom_components/<domain>/`; static assets in `www/`.

## Build, Test, and Development Commands
- Validate configuration from the HA host: `ha core check` (via SSH to the appliance). Use before commits.
- Reload without reboot: `ha core restart` after changes that affect core config.
- Optional local lint (if installed): `yamllint .` and `pre-commit run --all-files`.

## Coding Style & Naming Conventions
- YAML: 2-space indent; lowercase entity IDs; hyphen-separated automation/script IDs (e.g., `kitchen_lights_evening`).
- Templates: prefer Jinja filters over inline logic; keep macros in `templates/`.
- Custom components: follow Home Assistant style (async-first, type hints, `const.py` for keys).

## Testing Guidelines
- Automations/scripts: add `trace` description and `mode` to avoid race conditions.
- Use HA’s built-in trace viewer to verify new automations; export traces to `docs/traces/` only if anonymized.
- For custom components, add `tests/` with `pytest` where possible; run `pytest` locally in that component directory.

## Commit & Pull Request Guidelines
- Commit messages: concise imperative (“Add Zwave climate presets”). Group related file changes.
- PRs should state: summary, scope of devices/entities touched, screenshots for UI changes, and testing performed (`ha core check`, restarts, traces).
- Link to any related HA forum threads or issues when relevant.

## Security & Secrets
- Never commit `secrets.yaml`, tokens, or `.storage/`. Store credentials in `secrets.yaml` and reference with `!secret`.
- Regenerate keys before sharing the repo; scrub device IDs if exporting traces.

## Syncing with Home Assistant
- Quick pull (no runtime files) using tar-over-SSH: `ssh root@10.100.60.151 -p 22 "cd /config && tar -cf - --exclude-from=.gitignore ." | tar -xf -`
- If you prefer rsync: `/opt/homebrew/opt/rsync/bin/rsync -av --delete --exclude-from=.gitignore root@10.100.60.151:/config/ ./`
- After edits, push back the same way (or apply via Studio Code Server/SSH), then run `ha core check` and `ha core restart`.
