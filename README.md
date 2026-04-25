# Developer Notebook

A minimal Hugo portfolio and learning journal.

## Local development

```powershell
hugo server -D
```

Open the local URL Hugo prints, usually `http://localhost:1313`.

## Add a project

```powershell
hugo new projects/my-project.md
```

Project pages use this reflection format:

- What I built
- What I was trying to learn
- What went wrong
- Biggest challenge / bug
- What I learned
- What I would do differently
- GitHub link

## Add a blog note

```powershell
hugo new blog/my-note.md
```

## Deploy to GitHub Pages

Create a new empty repository on GitHub, then run these commands from this folder:

```powershell
git init
git add .
git commit -m "Initial portfolio site"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO.git
git push -u origin main
```

Then connect it to GitHub Pages:

1. Open the repository on GitHub.
2. Go to **Settings**.
3. Open **Pages** in the sidebar.
4. Under **Build and deployment**, choose **GitHub Actions**.
5. Push to `main` again whenever you want to publish changes.

Before publishing, update `baseURL`, `params.author`, and `params.github` in `hugo.toml`.

For a user site at `https://YOUR-USERNAME.github.io`, name the repository `YOUR-USERNAME.github.io`.
For a project site, any repository name is fine. The workflow will pass GitHub Pages' URL to Hugo during the build.
