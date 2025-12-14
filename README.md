# 🍊 Asset Crusher

**The ultimate GitHub Action to automatically compress images and optimize videos, saving precious repository space.**

> Stop committing 10MB PNGs. Let the Asset Crusher handle it.

Asset Crusher scans your repository for heavy media files, converts images to highly optimized **WebP** format, and compresses videos using **H.265 (HEVC)**. It can intelligently rewrite your commit history to ensure the heavy original files never bloat your `.git` folder.

---

## 🔥 Features

- 🖼️ **Images:** Automatically finds `.jpg`, `.jpeg`, and `.png` files and converts them to **WebP**.
- 🎬 **Videos:** Compresses `.mp4` files using `ffmpeg` with H.265 codec (CRF 28) for massive size reduction without visible quality loss.
- ☢️ **Nuclear Mode (Default):** Replaces the original file, **amends the last commit**, and force pushes. This means the heavy original file leaves **no trace** in your git history.
- 🛡️ **Safe Mode:** Keeps the original file, creates a new optimized version (or overwrites without history rewrite), and creates a standard new commit.
- ⚡ **Lightweight:** Runs on standard Ubuntu runners.

---

## 🚀 Usage

### Prerequisites
To allow the action to push changes back to your repository, you must enable **Read and write permissions** for workflows.
1. Go to your repository **Settings**.
2. Click **Actions** > **General**.
3. Under **Workflow permissions**, select **Read and write permissions**.

---

### 1. Default Mode (The "History Rewriter") ☢️
**Best for:** Keeping the repo size small.
It replaces original assets and **rewrites the commit history** so the original heavy files are gone forever.

Create a file at `.github/workflows/crush.yml`:

```yaml
name: Crush Assets
on:
  push:
    paths:
      - '**.jpg'
      - '**.png'
      - '**.mp4'
  workflow_dispatch:

permissions:
  contents: write # CRITICAL for pushing changes

jobs:
  crush:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # CRITICAL for amending commits

      - name: Run Asset Crusher
        uses: YOUR_USERNAME/asset-crusher@v1
        with:
          quality: '80' # Optional: WebP quality (0-100)
````

> **⚠️ Warning:** This mode performs a `git push --force`. Do not use this on shared branches (like `main`) if you are working in a large team where others might have pulled the code, unless everyone is aware of the history rewrite.

-----

### 2\. Safe Mode (No Force Push) 🛡️

**Best for:** Teams who want to keep original files or avoid force pushing.
This will create a **new commit** with the changes.

```yaml
name: Optimize Assets (Safe)
on: workflow_dispatch

permissions:
  contents: write

jobs:
  optimize:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Asset Crusher
        uses: YOUR_USERNAME/asset-crusher@v1
        with:
          delete_original: 'false' # Keep original files
          commit_message: '✨ Optimized assets [skip ci]'
```

-----

## ⚙️ Configuration (Inputs)

| Input | Description | Default |
| :--- | :--- | :--- |
| `quality` | The compression quality for WebP conversion (0-100). Lower is smaller. | `75` |
| `delete_original` | If `true`, deletes the source file and amends the commit (Rewrites History). If `false`, creates a standard commit. | `true` |
| `commit_message` | The commit message used when pushing changes. | `chore: assets optimized via Asset Crusher 🤖` |

-----

## 🛠️ Supported Formats

  - **Input:** `.jpg`, `.jpeg`, `.png` → **Output:** `.webp`
  - **Input:** `.mp4` → **Output:** `.mp4` (H.265 Optimized)

-----

## ⚖️ License

MIT License. Feel free to use and crush your assets\!
