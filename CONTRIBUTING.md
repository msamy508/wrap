# Contributing

Issues and pull requests are welcome. Keep changes small: one behaviour per pull request.

## What fits

- Making `/wrap` or `/handoff` more reliable, clearer, or safer.
- Better wording in `STATE.md` sections, the template, or the example.
- Install notes for an agent the Skills CLI supports.

What does not fit: scripts or dependencies, steps that push, steps that run code from the user's
repository, and new sections that most sessions would leave empty.

## Checklist

The rules are in [AGENTS.md](AGENTS.md#before-publishing-a-change). In short: update both copies of
the skill, keep every version number equal, and say in the pull request what you ran live.

## Releases

Install pinning uses git tags, not `metadata.version` alone:

1. Land the change on `main`.
2. Set the same semver in both `SKILL.md` files, `plugin.json`, and `marketplace.json`.
3. Tag it: `git tag -a v0.2.0 -m "v0.2.0"` and `git push origin v0.2.0`.
4. Users pin with `npx skills add msamy508/wrap@v0.2.0`.

A behaviour change in either skill is a minor bump; wording-only changes are a patch.
