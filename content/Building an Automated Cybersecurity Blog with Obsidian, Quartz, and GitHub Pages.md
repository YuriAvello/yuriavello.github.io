---
title: Building an Automated Cybersecurity Blog with Obsidian, Quartz, and GitHub Pages
date: 2026-05-26
tags:
  - homelab
  - obsidian
  - github
  - automation
draft: false
---



# Building an Automated Cybersecurity Blog with Obsidian, Quartz, and GitHub Pages

When i finish my academic journey with the MSc in cybersecurity from the University of Hertfordshire, i soon realized that i was not ready to enter the job market. Looking for some jobs advertisement I realized that I was missing lots of skill and to my surprise, certifications!

I wish that during my academic path someone would have told me about certs, so i could have started early to pursue them. After some research, I realized that certs was not the only thing I was missing, but also some hand-on experience with different tools, so I also decided to start to build my home lab.  

Ok Yuri, but what everything has to do with the topic of this post? Because, my friend, documentation is an integral part of many cybersecurity roles, and then why not write a blog of my journey that can also be showed up to future employer, and maybe inspire some of you?

Because I love use Obsidian for note taking, I wanted a solution that would allow me to auto publish every time I would write something for the blog in Obsidian, so here step by step what I did to achieve the result! 

I hope this may help! Good reading!

---

## The Stack

- **Obsidian** — note-taking app where I write everything
- **Quartz v5** — static site generator that turns Markdown into a beautiful website
- **GitHub Pages** — free hosting, triggered automatically on every push
- **GitHub Actions** — builds and deploys the site on every commit
- **Windows Task Scheduler + PowerShell** — pushes changes to GitHub automatically every 30 minutes

The end result: I write a note in Obsidian, and within 30 minutes it's live on the internet at [yuriavello.github.io](https://yuriavello.github.io/) without me doing anything else.

---

## Prerequisites

Before starting, make sure you have these installed:

- [Git](https://git-scm.com/) — verify with `git --version`
- [Node.js v18+](https://nodejs.org/) — verify with `node --version`
- An [Obsidian](https://obsidian.md/) vault already set up
- A [GitHub](https://github.com/) account

---

## Step 1 — Set Up Quartz

Navigate to where you want your blog files to live and clone Quartz:

```bash
d:
cd Blog
git clone https://github.com/jackyzha0/quartz.git
cd quartz
npm i
```

Then initialise Quartz:

```bash
npx quartz create
```

When prompted:

- Choose **Blog** as the template
- Choose **Symlink an existing folder** for content (points directly to your Obsidian folder)
- Enter the full path to your Obsidian blog folder, e.g. `D:\Obsidian Vault\Yuri\07. Blog`
- Choose **Treat links as shortest path**
- Enter your GitHub Pages URL: `yourusername.github.io`

> **Note:** On Windows, symlinks require running the terminal as Administrator. If you get a permissions error, close and reopen Command Prompt as Administrator and try again.

---

## Step 2 — Create an index.md

Quartz needs an `index.md` file as the homepage. Create one inside your Obsidian blog folder:

```markdown
---
title: Your Blog Name
---

# Welcome

A short intro about yourself and what this blog covers.
```

---

## Step 3 — Preview Locally

Test that everything looks right before going live:

```bash
npx quartz build --serve
```

Open `http://localhost:8080` in your browser. You should see your blog with the index page and any posts you've created.

---

## Step 4 — Create the GitHub Repository

Go to [github.com/new](https://github.com/new) and create a repository named exactly:

```
yourusername.github.io
```

Keep it **Public** and do not add a README.

---

## Step 5 — Connect Quartz to GitHub

Generate a Personal Access Token on GitHub:

1. Go to **Settings → Developer settings → Personal access tokens → Tokens (classic)**
2. Click **Generate new token (classic)**
3. Give it a name, set no expiration, tick **repo** and **workflow**
4. Copy the token immediately

Then connect your local Quartz to GitHub:

```bash
git remote set-url origin https://YOURUSERNAME:YOUR_TOKEN@github.com/YOURUSERNAME/yourusername.github.io.git
git branch -M main
npx quartz sync --no-pull
```

---

## Step 6 — Configure GitHub Pages

In your GitHub repo go to **Settings → Pages** and set Source to **GitHub Actions**.

---

## Step 7 — Add the Deployment Workflow

In your GitHub repo, navigate to `.github/workflows/` and create a new file called `deploy.yml` with this content:

```yaml
name: Deploy Quartz site to GitHub Pages

on:
  push:
    branches:
      - main

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - name: Install Dependencies
        run: npm ci
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public

  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

Commit this file directly on GitHub. This triggers a build — check the **Actions** tab to watch it deploy. Once it goes green, your blog is live!

---

## Step 8 — Automate Publishing with PowerShell

Rather than manually pushing every time you write, a PowerShell script run by Windows Task Scheduler handles this automatically.

Create the script at `D:\Blog\autopublish.ps1`:

```powershell
Set-Location "D:\Blog\quartz"

$status = git status --porcelain
if ($status) {
    git add .
    git commit -m "Auto-publish $(Get-Date -Format 'yyyy-MM-dd HH:mm')"
    git push
}
```

This only commits when there are actual changes — no empty commits cluttering your history.

### Setting Up Task Scheduler

1. Open **Windows Task Scheduler**
2. Click **Create Basic Task**
3. Name it `Quartz Auto Publish`
4. Set trigger to **Daily**, then set it to repeat every **30 minutes** indefinitely
5. Set action to **Start a program**:
    - Program: `powershell.exe`
    - Arguments: `-ExecutionPolicy Bypass -WindowStyle Hidden -File D:\Blog\autopublish.ps1`
6. Click **Finish**

Now every 30 minutes, if you've written or updated any notes in your Obsidian blog folder, they'll automatically push to GitHub and your blog will rebuild and deploy.

---

## Why Not Obsidian Git?

Obsidian Git is a popular plugin that commits your vault to GitHub automatically. The problem is it pushes your **entire Obsidian vault** — including private notes, diary entries, and personal documents. Since my Quartz repo is separate from my vault, Task Scheduler pushing just `D:\Blog\quartz` is the safer and cleaner approach.

---

## The Final Workflow

```
Write note in Obsidian (07. Blog folder)
        ↓
Symlink reflects changes instantly in Quartz content folder
        ↓
Task Scheduler runs every 30 minutes
        ↓
PowerShell script detects changes and pushes to GitHub
        ↓
GitHub Actions builds and deploys the site
        ↓
Live at yourusername.github.io within minutes
```

---

---

## Troubleshooting — Symlink Not Working on Windows

After setting up the symlink, I noticed the `D:\Blog\quartz\content` folder was showing as regular files instead of an actual symlink, meaning changes in Obsidian were not being picked up by Quartz automatically.

### The Fix

Open **PowerShell as Administrator** and run:

```powershell
Remove-Item "D:\Blog\quartz\content" -Recurse -Force

New-Item -ItemType SymbolicLink -Path "D:\Blog\quartz\content" -Target "D:\Obsidian Vault\Yuri\07. Blog"
```

Verify it worked with:

```powershell
Get-Item "D:\Blog\quartz\content" | Select-Object Name, LinkType, Target
```

You should see `LinkType: SymbolicLink` pointing to your Obsidian blog folder.

> ⚠️ **Warning:** The `Remove-Item` command will delete the `content` folder including any files inside it. If your `index.md` lives inside `D:\Blog\quartz\content` rather than directly in your Obsidian folder, it will be deleted and your website will break. Before running this, make sure your `index.md` is saved inside your Obsidian `07. Blog` folder, not just inside the Quartz content folder.

### Testing Auto-Publish

After fixing the symlink, test the publish script manually:

```powershell
& "D:\Blog\autopublish.ps1"
```

If everything is working you should see it detect changes, commit, and push to GitHub. The Actions tab on your GitHub repo should then show a new deployment running.


---

## Giscus Comments Setup

### Problem

Blog was showing: **"An error occurred: giscus is not installed on this repository"**

### Steps to fix

**1. Enable GitHub Discussions**

- Went to `yuriavello.github.io` repo → Settings → Features → ticked **Discussions**
- Clicked **Set up discussions** to initialise it

**2. Install Giscus app**

- Went to [github.com/apps/giscus](https://github.com/apps/giscus)
- Installed it on `YuriAvello/yuriavello.github.io` repo only

**3. Generate config at giscus.app**

- Entered `YuriAvello/yuriavello.github.io` as the repository
- Selected **Announcements** as the discussion category
- Got the repo ID and category ID from the generated script

**4. Updated `quartz.config.yaml`** Found the comments plugin section and replaced the TODO placeholders:

```yaml
- source: github:quartz-community/comments
  enabled: true
  options:
    provider: giscus
    options:
      repo: YuriAvello/yuriavello.github.io
      repoId: R_kgDOSn5cCQ
      category: Announcements
      categoryId: DIC_kwDOSn5cCc4C92vp
      mapping: pathname
      strict: false
      reactionsEnabled: true
      inputPosition: bottom
```

**5. Pushed to GitHub**

```powershell
& "D:\Blog\autopublish.ps1"
```

---


## What's Next

This is just the foundation. Future posts will cover:

- Customising the Quartz theme with custom colours
- Setting dark mode as default
- Writing effective SOC investigation writeups
- Documenting homelab builds as blog posts

If you're building a cybersecurity portfolio, I'd strongly recommend starting a blog like this. The process of writing forces you to consolidate what you've learned, and having it public gives recruiters something concrete to look at beyond a CV.

---

_This blog documents my journey from hospitality worker to cybersecurity professional. Follow along at [yuriavello.github.io](https://yuriavello.github.io/)._