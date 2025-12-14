# 🍊 Asset Crusher

**The ultimate GitHub Action to automatically compress images, optimize videos, and update code references.**

> Stop committing 10MB PNGs. Let the Asset Crusher handle it.

Asset Crusher scans your repository for heavy media files, converts images to highly optimized **WebP** format, and compresses videos using **H.265 (HEVC)**.

But it doesn't stop there. It acts as a **Smart Refactor Tool**: when it converts an image, it automatically searches your source code (HTML, CSS, JS, etc.) and updates the file references so your project doesn't break.

---

## 🔥 Features

- 🖼️ **Images:** Automatically finds `.jpg`, `.jpeg`, and `.png` files and converts them to **WebP**.
- 📝 **Auto-Refactor:** Converting `logo.png` to `logo.webp`? The action automatically hunts down `logo.png` references in your code (`.html`, `.css`, `.js`, `.tsx`, etc.) and updates them.
- 🎬 **Videos:** Compresses `.mp4` files using `ffmpeg` with H.265 codec (CRF 28) for massive size reduction without visible quality loss.
- ☢️ **Nuclear Mode (Default):** Replaces the original file, **amends the last commit**, and force pushes. Heavy original files leave **no trace** in your git history.
- 🛡️ **Safe Mode:** Keeps the original file, creates a new optimized version alongside it, and creates a standard new commit.
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
**Best for:** Keeping the repo size small and code up-to-date.
It replaces original assets, **updates your code references**, and **rewrites the commit history** so the original heavy files are gone forever.

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
      - uses: actions/checkout@v6
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
This will create a **new commit** with the new assets.
*Note: Auto-Refactor is disabled in this mode to prevent breaking changes with the original files.*

```yaml
name: Optimize Assets (Safe)
on: workflow_dispatch

permissions:
  contents: write

jobs:
  optimize:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6

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
| `delete_original` | If `true`: Deletes source file, **updates code references**, and amends history. If `false`: Keeps source, no refactor, standard commit. | `true` |
| `commit_message` | The commit message used when pushing changes. | `chore: assets crushed & code refactored 🤖` |

-----

## 🧠 Smart Refactoring Support

When running in **Default Mode**, Asset Crusher scans the following file types for image references and updates them to the new `.webp` extension:
`html`, `css`, `js`, `jsx`, `ts`, `tsx`, `vue`, `svelte`, `php`, `md`, `json`.

**Example:**

  * **Before:** `<img src="./assets/banner.png" />`
  * **After:** `<img src="./assets/banner.webp" />`

-----

## 🛠️ Supported Formats

  - **Input:** `.jpg`, `.jpeg`, `.png` → **Output:** `.webp`
  - **Input:** `.mp4` → **Output:** `.mp4` (H.265 Optimized)

-----

## ⚖️ License

MIT License. Feel free to use and crush your assets\!
