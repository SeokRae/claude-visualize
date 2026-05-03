# Contributing to claude-visualize

## What's welcome

- New visualization types or templates
- Improvements to existing skill logic
- Bug reports from real usage
- Documentation improvements

## How to contribute

1. Open an Issue first — describe what you want to change and why
2. Fork the repo and create a branch: `feature/{issue-number}-{description}`
3. Edit the relevant `skills/{skill-name}/SKILL.md`
4. Open a PR with `Closes #N` in the body

## Editing skills

Each skill lives in `skills/{skill-name}/SKILL.md`.

The frontmatter fields that matter most:

| Field | Effect |
|-------|--------|
| `description` | Controls when Claude auto-triggers this skill — most impactful |
| `allowed-tools` | Tools the skill can use — keep to minimum needed |
| `name` | Must match the directory name |

## Adding a new skill

1. Create `skills/{skill-name}/SKILL.md` with required frontmatter
2. Add the skill path to `.claude-plugin/plugin.json` → `skills` array
3. Bump the version in both `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` (see Versioning below)
4. Document the skill in `README.md` and `README.ko.md`
5. Add examples to `examples/` if applicable

## Versioning

This project uses [Semantic Versioning](https://semver.org/) (`MAJOR.MINOR.PATCH`).

| Change type | Version segment | Example |
|-------------|----------------|---------|
| New skill or visualization type added | `MINOR` | 1.0.1 → 1.1.0 |
| Existing skill behavior extended | `MINOR` | 1.0.1 → 1.1.0 |
| Bug fix, wording, doc only | `PATCH` | 1.0.1 → 1.0.2 |
| Skill removed or interface broken | `MAJOR` | 1.x.x → 2.0.0 |

**Rule**: version bump must be included in the **same PR** as the change — never as a separate follow-up PR.

Files to update together (when both exist):

```
.claude-plugin/plugin.json      → "version"
.claude-plugin/marketplace.json → "plugins[0].version"
```

## Release workflow

After a PR is merged:

```bash
# 1. Pull latest main
git checkout main && git pull origin main

# 2. Create annotated tag
git tag -a v{version} -m "v{version}: {one-line summary}"

# 3. Push tag
git push origin v{version}

# 4. Create GitHub Release
gh release create v{version} --title "v{version}" --generate-notes
```

## Reporting issues

Open a GitHub Issue with:
- Which skill was involved
- What you expected to happen
- What actually happened
