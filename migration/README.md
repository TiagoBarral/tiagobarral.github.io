# New Computer Migration

This temporary folder contains the background and fallback files needed to continue the portfolio on a new computer.

## Next Steps

### 1. Install Git and Hugo

Open PowerShell:

```powershell
winget install --id Git.Git -e
winget install --id Hugo.Hugo.Extended -e
```

Restart PowerShell after installation.

### 2. Clone the full project

```powershell
git clone https://github.com/TiagoBarral/tiagobarral.github.io.git
cd tiagobarral.github.io
```

### 3. Start the website

```powershell
hugo server -D
```

Open:

```text
http://localhost:1313/
```

### 4. Start a new Codex session

Give Codex this instruction:

```text
Read AGENTS.md and HANDOFF.md before making changes.
```

### 5. Publish future changes

```powershell
hugo --gc
git add .
git commit -m "describe the change"
git push origin main
```

## Included Files

- `portfolio-main-2b82b71.zip` — fallback snapshot of the complete project at the editorial redesign merge commit.
- `context/AGENTS.md` — persistent rules for future coding agents.
- `context/HANDOFF.md` — project background, design decisions, architecture, and continuation instructions.
- `context/PORTFOLIO_UPDATE_MODEL.md` — templates and guidance for keeping the portfolio current.

Snapshot SHA-256:

```text
D91F81461F87E0A1C64F2BB9125FC25EE90F9C672643110FD49D2AC7A943F395
```

The normal Git clone already contains the full working project. The ZIP is only a fallback and normally does not need to be extracted.

## After The New Computer Is Working

Remove this temporary package and ignore future migration folders:

```powershell
git rm -r migration
Add-Content -Path .gitignore -Value "migration/"
git add .gitignore
git commit -m "chore: remove migration package"
git push origin main
```

Important: because this folder is tracked during migration, adding it to `.gitignore` alone will not remove it. Run `git rm` first, then add the ignore rule.

## Not Included

The migration package intentionally excludes generated or private machine-local data:

- `public/`
- `resources/`
- `.hugo_build.lock`
- Hugo server logs
- environment files, tokens, credentials, and private journals
