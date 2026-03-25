# MkDocs And GitHub Pages Migration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the current Docsify-based tutorial site with an MkDocs Material site and publish it through GitHub Actions to GitHub Pages.

**Architecture:** Keep the existing tutorial Markdown and image assets under `docs/`, replace Docsify entrypoints with a root `mkdocs.yml` plus a generated `docs/index.md` homepage, and apply only the minimum compatibility edits required for Material. Build and deployment should use exact-pinned Python dependencies from `requirements.txt` and the official GitHub Pages artifact workflow in `.github/workflows/docs.yml`.

**Tech Stack:** MkDocs, mkdocs-material, pymdown-extensions, GitHub Actions, GitHub Pages, Markdown, YAML

---

## File Structure

- Create: `mkdocs.yml`
  Responsibility: site metadata, theme configuration, Markdown extensions, and navigation.
- Create: `requirements.txt`
  Responsibility: exact direct dependency pins for local builds and CI.
- Create: `docs/index.md`
  Responsibility: MkDocs homepage, migrated from the current `docs/home.md`.
- Create: `.github/workflows/docs.yml`
  Responsibility: build the site on pushes to `main` and deploy the built `site/` artifact to GitHub Pages.
- Modify: `README.md`
  Responsibility: repository overview, local preview commands, strict build command, and GitHub Pages setup note.
- Modify: `docs/zh-cn/README.md`
  Responsibility: replace Docsify-only admonition syntax with Material-compatible admonition syntax.
- Delete: `docs/index.html`
  Responsibility removed: Docsify app entrypoint.
- Delete: `docs/_sidebar.md`
  Responsibility removed: Docsify navigation source.
- Delete: `docs/home.md`
  Responsibility removed: Docsify homepage source, replaced by `docs/index.md`.

### Task 1: Bootstrap MkDocs Dependencies And Root Configuration

**Files:**
- Create: `requirements.txt`
- Create: `mkdocs.yml`

- [ ] **Step 1: Capture the current baseline failure**

Run: `python3 -m mkdocs build --strict`
Expected: FAIL because MkDocs is not installed yet or because `mkdocs.yml` does not exist.

- [ ] **Step 2: Create `requirements.txt` with exact direct pins**

```txt
mkdocs==1.6.1
mkdocs-material==9.7.6
pymdown-extensions==10.21
```

- [ ] **Step 3: Install the pinned dependencies**

Run: `python3 -m pip install -r requirements.txt`
Expected: PASS with the three direct packages installed successfully.

- [ ] **Step 4: Create `mkdocs.yml` with the initial site configuration**

```yaml
site_name: 简明COSBench教程
site_description: A Byte of COSBench
site_url: https://sine-io.github.io/byte-of-cosbench/
repo_url: https://github.com/sine-io/byte-of-cosbench
repo_name: sine-io/byte-of-cosbench

theme:
  name: material
  language: zh
  features:
    - navigation.sections
    - navigation.top
    - content.code.copy

plugins:
  - search

markdown_extensions:
  - admonition
  - attr_list
  - md_in_html
  - tables
  - toc:
      permalink: true
  - pymdownx.details
  - pymdownx.superfences

nav:
  - 首页: index.md
  - 说明: zh-cn/README.md
  - 新手村:
      - COSBench认识、使用、结果分析: zh-cn/zero/01-quickstart.md
  - 进阶营:
      - COSBench开发环境配置: zh-cn/hero/02-dev-envs-config.md
      - COSBench源码分析: zh-cn/hero/03-cosbench-source-code.md
      - COSBench 开发一个适配器: zh-cn/hero/04-adapter-development.md
  - 附录:
      - COSBench常用测试模型: zh-cn/appendix/awesome-testing-models.md
```

- [ ] **Step 5: Run a strict build to confirm the next missing piece is the homepage**

Run: `python3 -m mkdocs build --strict`
Expected: FAIL with a missing file error for `docs/index.md` or the `index.md` nav target.

- [ ] **Step 6: Commit the configuration bootstrap**

```bash
git add requirements.txt mkdocs.yml
git commit -m "build: add mkdocs site configuration"
```

### Task 2: Migrate The Homepage To Standard MkDocs Content

**Files:**
- Create: `docs/index.md`
- Delete: `docs/home.md`

- [ ] **Step 1: Create `docs/index.md` from the existing homepage content**

```md
# 简明COSBench教程

英文名是《A Byte of COSBench》，源于《A Byte of Python》，向作者致敬！

如果你需要评估对象存储的性能，那么可以使用 `COSBench`。

如果你想知道 `COSBench` 是什么、怎么用、怎么分析结果等等，可以阅读**新手村**里的文章。

如果你想深入了解 `COSBench`、开发自己的适配器等等，可以阅读**进阶营**里的文章。
```

- [ ] **Step 2: Remove the obsolete Docsify homepage source**

Run: `rm docs/home.md`
Expected: PASS with `docs/home.md` removed from the working tree.

- [ ] **Step 3: Build the site again to confirm the MkDocs homepage resolves**

Run: `python3 -m mkdocs build --strict`
Expected: PASS or reveal only content-level compatibility issues that are unrelated to `index.md` being missing.

- [ ] **Step 4: Commit the homepage migration**

```bash
git add docs/index.md docs/home.md
git commit -m "docs: migrate homepage to mkdocs index"
```

### Task 3: Remove Docsify Entrypoints And Fix Markdown Compatibility

**Files:**
- Modify: `docs/zh-cn/README.md`
- Delete: `docs/index.html`
- Delete: `docs/_sidebar.md`

- [ ] **Step 1: Replace the Docsify warning callout in `docs/zh-cn/README.md`**

Replace the trailing Docsify-only line:

```md
!> 请勿出售本书的电子版或印刷版，除非您明确指出相关内容并非来自本书。<br />如有对书中内容的引用，请注明出处，非常感谢。
```

with a Material-compatible admonition:

```md
!!! warning

    请勿出售本书的电子版或印刷版，除非您明确指出相关内容并非来自本书。
    如有对书中内容的引用，请注明出处，非常感谢。
```

- [ ] **Step 2: Remove the obsolete Docsify entrypoint files**

Run: `rm docs/index.html docs/_sidebar.md`
Expected: PASS with both Docsify-only files removed from the working tree.

- [ ] **Step 3: Verify there are no remaining Docsify callout markers in published docs**

Run: `rg -n '^!>|^\?>' docs/zh-cn README.md`
Expected: no matches.

- [ ] **Step 4: Run a strict build to verify the migrated content renders cleanly**

Run: `python3 -m mkdocs build --strict`
Expected: PASS and generate the `site/` directory without navigation or Markdown errors.

- [ ] **Step 5: Commit the compatibility cleanup**

```bash
git add docs/zh-cn/README.md docs/index.html docs/_sidebar.md
git commit -m "docs: remove docsify-specific files"
```

### Task 4: Add The GitHub Pages Workflow

**Files:**
- Create: `.github/workflows/docs.yml`

- [ ] **Step 1: Create the GitHub Pages workflow**

```yaml
name: docs

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: github-pages
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v5

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Configure GitHub Pages
        uses: actions/configure-pages@v5

      - name: Install dependencies
        run: python -m pip install --upgrade pip && pip install -r requirements.txt

      - name: Build site
        run: python -m mkdocs build --strict

      - name: Upload GitHub Pages artifact
        uses: actions/upload-pages-artifact@v4
        with:
          path: site

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build

    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: Parse the workflow YAML locally after dependency installation**

Run: `python3 -c 'import pathlib, yaml; data = yaml.safe_load(pathlib.Path(".github/workflows/docs.yml").read_text()); print(sorted(data["jobs"].keys()))'`
Expected: PASS and print `['build', 'deploy']`.

- [ ] **Step 3: Re-run the strict site build so the workflow and site configuration are validated together**

Run: `python3 -m mkdocs build --strict`
Expected: PASS.

- [ ] **Step 4: Commit the deployment workflow**

```bash
git add .github/workflows/docs.yml
git commit -m "ci: add github pages deployment workflow"
```

### Task 5: Update The Repository README And Run Final Verification

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Replace the current minimal README with a repository-oriented version**

````md
# 简明COSBench教程

英文名是《A Byte of COSBench》。

这个仓库收录了 COSBench 教程文档，并使用 MkDocs Material 构建静态站点。

## 本地预览

```bash
python3 -m pip install -r requirements.txt
python3 -m mkdocs serve
```

## 构建检查

```bash
python3 -m mkdocs build --strict
```

## 发布

推送到 `main` 后，GitHub Actions 会自动构建并部署文档到 GitHub Pages。

首次启用时，请在仓库 `Settings -> Pages` 中将发布来源设置为 `GitHub Actions`。
````

- [ ] **Step 2: Run the strict build as the primary acceptance test**

Run: `python3 -m mkdocs build --strict`
Expected: PASS.

- [ ] **Step 3: Run a short local serve smoke test**

Run: `timeout 5s python3 -m mkdocs serve -a 127.0.0.1:8000`
Expected: PASS long enough to print that the development server is serving at `http://127.0.0.1:8000/`, then exit because of the timeout.

- [ ] **Step 4: Check for whitespace and patch hygiene issues**

Run: `git diff --check`
Expected: no output.

- [ ] **Step 5: Review the final file set**

Run: `git status --short`
Expected: only the intended MkDocs migration files appear as modified, created, or deleted.

- [ ] **Step 6: Commit the finished migration**

```bash
git add README.md mkdocs.yml requirements.txt docs/index.md docs/zh-cn/README.md .github/workflows/docs.yml docs/index.html docs/_sidebar.md docs/home.md
git commit -m "docs: migrate site from docsify to mkdocs"
```
