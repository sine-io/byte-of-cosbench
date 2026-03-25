# MkDocs And GitHub Pages Design

## Summary

This document defines how to migrate the existing COSBench tutorial site from Docsify to MkDocs, using the Material theme, and how to publish the generated site with GitHub Actions and GitHub Pages.

The current repository already stores tutorial content as Markdown under `docs/zh-cn/` and image assets under `docs/zh-cn/_images/`. The migration should preserve that content layout as much as possible while replacing Docsify-specific entrypoints and navigation with standard MkDocs configuration.

## Goals

- Convert the tutorial site from Docsify to MkDocs.
- Use `mkdocs-material` as the documentation theme.
- Preserve the existing article files and image paths wherever possible.
- Rebuild the current navigation structure in `mkdocs.yml`.
- Add a GitHub Actions workflow that builds and deploys the site to GitHub Pages.
- Document the local preview and deployment setup in the repository README.

## Non-Goals

- Rewriting or substantially editing tutorial content.
- Introducing a blog, comments, versioned docs, or multilingual plugins.
- Restructuring the article hierarchy beyond what is needed for MkDocs.
- Publishing currently hidden chapters `05-add-maven.md` and `06-add-k8s.md`.

## Current State

The repository currently serves documentation through Docsify:

- `docs/index.html` is the Docsify entrypoint.
- `docs/_sidebar.md` defines the navigation.
- `docs/home.md` acts as the site homepage.
- Tutorial content is stored in `docs/zh-cn/`.
- Images are stored in `docs/zh-cn/_images/`.

This means the site content is already in a format MkDocs can consume, but the rendering, navigation, homepage entrypoint, and deployment model are Docsify-specific.

## Proposed Site Structure

The repository should move to a standard MkDocs layout while keeping the existing content files in place.

### Content Layout

- Keep `docs/` as the MkDocs documentation root.
- Create `docs/index.md` as the MkDocs homepage using the content from the current `docs/home.md`.
- Keep tutorial articles in `docs/zh-cn/zero/`, `docs/zh-cn/hero/`, and `docs/zh-cn/appendix/`.
- Keep image assets in `docs/zh-cn/_images/`.
- Keep `docs/zh-cn/README.md` as the "说明" page.

### Remove Docsify Entrypoints

The following Docsify-specific files should be removed because MkDocs will replace their responsibilities:

- `docs/index.html`
- `docs/_sidebar.md`

The file `docs/home.md` should also be removed after its content is migrated into `docs/index.md`.

## Navigation Design

Navigation should be declared explicitly in `mkdocs.yml` and mirror the current information architecture.

### Top-Level Navigation

- 首页: `index.md`
- 说明: `zh-cn/README.md`
- 新手村
- 进阶营
- 附录

### Chapter Navigation

The chapter ordering should be:

- 新手村
  - `zh-cn/zero/01-quickstart.md`
- 进阶营
  - `zh-cn/hero/02-dev-envs-config.md`
  - `zh-cn/hero/03-cosbench-source-code.md`
  - `zh-cn/hero/04-adapter-development.md`
- 附录
  - `zh-cn/appendix/awesome-testing-models.md`

The files `zh-cn/hero/05-add-maven.md` and `zh-cn/hero/06-add-k8s.md` should remain in the repository but stay excluded from navigation because they are currently hidden from the published site as well.

## Theme And UX

The site should use `mkdocs-material` with a restrained configuration focused on readability and documentation ergonomics rather than visual customization.

### Required Theme Capabilities

- Built-in full-text search.
- Left navigation sidebar.
- Right-side table of contents for long articles.
- Syntax highlighting for shell, Java, XML, YAML, and Markdown code blocks.
- Code copy button.
- GitHub repository link in the header.

### Theme Direction

- Site name: `简明COSBench教程`
- Language: Simplified Chinese
- Theme: `material`
- Keep visual customization light; rely on Material defaults unless a setting directly improves readability or navigation.

## Markdown Compatibility Adjustments

The existing content should be updated only where required for correct MkDocs and Material rendering.

### Required Adjustments

- Convert the Docsify homepage source from `docs/home.md` to `docs/index.md`.
- Replace Docsify-specific admonition syntax with Material-compatible admonition syntax.
- Apply the following deterministic mapping for callouts encountered during migration:
  - `!>` to `!!! warning`
  - `?>` to `!!! tip`
- Verify relative links and image references after the homepage move.
- Preserve article body content unless a change is required for valid rendering or navigation.

### Compatibility Principle

Only make compatibility edits that prevent broken rendering, broken links, or broken navigation. Avoid cosmetic rewrites.

## Repository Configuration

The repository root should gain a standard MkDocs configuration and dependency file.

### `mkdocs.yml`

This file should define:

- `site_name`
- `site_description`
- `site_url`
- `repo_url`
- `repo_name`
- `theme`
- `nav`
- Markdown extensions required for admonitions and code rendering
- Plugins required for search

The expected Markdown extensions should include at least:

- `admonition`
- `attr_list`
- `tables`
- `toc`
- `pymdownx.details`
- `pymdownx.superfences`

The canonical site URL should target the GitHub Pages endpoint for this repository:

- `https://sine-io.github.io/byte-of-cosbench/`

### `requirements.txt`

This file should use exact version pins rather than open ranges so local builds and CI stay reproducible. At minimum it should include:

- `mkdocs`
- `mkdocs-material`

## GitHub Actions And GitHub Pages Deployment

The site should be published using the current GitHub Pages Actions flow rather than a manually managed `gh-pages` branch.

### Workflow File

Create:

- `.github/workflows/docs.yml`

### Workflow Triggers

- On `push` to `main`
- On manual dispatch through `workflow_dispatch`

### Workflow Steps

1. Check out the repository.
2. Set up Python.
3. Install dependencies from `requirements.txt`.
4. Run `mkdocs build --strict`.
5. Configure GitHub Pages.
6. Upload the generated site as a Pages artifact.
7. Deploy the artifact to GitHub Pages.

### Workflow Permissions

The workflow should declare:

- `contents: read`
- `pages: write`
- `id-token: write`

### Concurrency

Add a deployment concurrency group so repeated pushes do not create overlapping Pages deployments.

### GitHub Repository Settings

The repository should use GitHub Pages with source set to `GitHub Actions`. This requirement should be documented in `README.md`, because it is configured in the GitHub repository settings rather than in the working tree.

## README Updates

The repository `README.md` should be expanded from its current short introduction to also include:

- A brief project description.
- Local development commands:
  - install dependencies
  - run `mkdocs serve`
  - run `mkdocs build --strict`
- A note that deployment is handled by GitHub Actions.
- A note that GitHub Pages must be enabled with source `GitHub Actions`.

The README should remain concise and repository-oriented. It does not need to duplicate the full tutorial homepage content.

## Implementation Boundaries

This migration should remain intentionally narrow:

- Do not rewrite article prose for style.
- Do not split content into additional directories unless required by MkDocs.
- Do not add extra MkDocs plugins unless they solve a concrete rendering or deployment need.
- Do not change the published chapter set beyond reproducing the current visible navigation.

## Verification Strategy

Implementation is not complete until the site is verified both locally and in CI.

### Local Verification

- Run `mkdocs build --strict` and fix all reported issues.
- If the environment permits, run `mkdocs serve` to confirm the site starts successfully.

### CI Verification

- The GitHub Actions workflow must build successfully on the repository default branch.
- The Pages deployment job must publish the built site artifact without errors.

### Acceptance Criteria

The migration is successful when all of the following are true:

- The repository contains a valid `mkdocs.yml`.
- The site builds with `mkdocs build --strict`.
- The homepage is served from `docs/index.md`.
- Existing published chapters appear in the intended navigation order.
- Images render correctly.
- Docsify-only files are removed.
- The GitHub Actions workflow deploys the site to GitHub Pages.

## Risks

The most likely implementation issues are:

- Docsify-specific Markdown syntax rendering differently in Material.
- Relative paths breaking after the homepage migration from `home.md` to `index.md`.
- Missing MkDocs extensions leading to degraded rendering of notes or code blocks.

These risks should be handled through strict local builds and targeted Markdown compatibility fixes, not through broad rewrites.

## Recommended Implementation Sequence

1. Add `mkdocs.yml` and `requirements.txt`.
2. Create `docs/index.md` from the current homepage content.
3. Remove Docsify-only files and migrate any remaining Docsify-only syntax.
4. Update `README.md` with local preview and deployment guidance.
5. Add `.github/workflows/docs.yml`.
6. Run local verification with `mkdocs build --strict`.

This sequence keeps the content migration, site configuration, and deployment setup easy to validate incrementally.
