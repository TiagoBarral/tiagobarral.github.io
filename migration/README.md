# New Computer Migration Package

This folder is a temporary transfer package for the editorial portfolio redesign.

## What Is Included

- `portfolio-editorial-snapshot-713a323.zip` — complete repository snapshot from commit `713a323`, including all committed content, templates, CSS, shortcodes, images, and the `hugo-port/` design reference.
- `context/AGENTS.md` — persistent repository rules for coding agents.
- `context/HANDOFF.md` — background from the design conversation, current architecture, decisions, and continuation instructions.
- `context/PORTFOLIO_UPDATE_MODEL.md` — the model and templates for keeping Projects, Journal, Guides, and Now current.

Snapshot SHA-256:

```text
D8C2A37BFD1268288773F51C59735BA056967408C67AB06035365E34B093367E
```

## Recommended Setup On The New Computer

The Git branch remains the source of truth. Clone it first:

```powershell
git clone https://github.com/TiagoBarral/tiagobarral.github.io.git
Set-Location tiagobarral.github.io
git switch redesign/editorial-port
hugo server -D
```

Then read:

```text
AGENTS.md
HANDOFF.md
PORTFOLIO_UPDATE_MODEL.md
```

The ZIP is a fallback copy. You should not need to extract it after a successful clone, but it guarantees that the exact migration snapshot is available inside this folder.

## Verification

After cloning:

```powershell
git status
git log -1 --oneline
hugo --gc
```

The expected migration commit is:

```text
713a323 chore: snapshot editorial redesign handoff
```

## Remove This Package After Migration

Once the new computer is working and the snapshot has been verified, remove this temporary package from Git and ignore future local migration packages.

Run from the repository root:

```powershell
git rm -r migration
Add-Content -Path .gitignore -Value "migration/"
git add .gitignore
git commit -m "chore: remove migration package"
git push origin redesign/editorial-port
```

Important: adding `migration/` to `.gitignore` before `git rm` is not enough because this folder is already tracked. Remove it from Git first, then ignore it.

## Not Included

Generated and machine-local files remain excluded:

- `public/`
- `resources/`
- `.hugo_build.lock`
- Hugo server logs
- private journals, environment files, credentials, and tokens
