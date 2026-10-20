# Movian Plugins

Collection of [Movian](https://github.com/andoma/movian) / Showtime plugins maintained in this organization.

Each repository is a self-contained plugin that can be installed by downloading its release zip and opening it from Movian (USB / network share / local folder).

---

## Plugins

| Plugin | Description | Latest release |
|--------|-------------|----------------|
| [fshare](https://github.com/m-plugs/movian-plugin-fshare) | Browse and play content from Fshare folders | [Releases](https://github.com/m-plugs/movian-plugin-fshare/releases) |
| [rap](https://github.com/m-plugs/movian-plugin-rap) | Browse and play your self-hosted movie library (MediaIndex) | [Releases](https://github.com/m-plugs/movian-plugin-rap/releases) |

> Add a new row for every plugin you publish under this org.

---

## Install a plugin

1. Open the plugin’s **Releases** page and download the latest  
   `<plugin-id>-<version>.zip` asset (do **not** unzip it).
2. Copy the zip to a USB stick, network share, or any folder Movian can see.
3. In Movian go to **USB** (or the corresponding source) → select the zip → **Install**.
4. The plugin appears on the home screen / under its category.

**Direct download URL pattern**

```
https://github.com/<owner>/<repo>/releases/download/<version>/<plugin-id>-<version>.zip
```

Example:

```
https://github.com/m-plugs/movian-plugin-fshare/releases/download/0.1.9/fshare-0.1.9.zip
```

---

## Plugin repository layout

Every plugin repo follows the same minimal structure:

```
movian-plugin-<id>/
├── plugin.json          # Manifest (id, version, title, icon, …)
├── <main>.js            # Entry point listed in plugin.json "file"
├── logo.png / icon.png  # Plugin icon
├── settings.js          # Optional – user settings (proxy URL, …)
├── lib/                 # Optional – helper modules
├── views/               # Optional – custom GLW layouts
└── .github/workflows/
    └── release.yml      # Thin caller → central release workflow
```

### `plugin.json` essentials

```json
{
  "id": "fshare",
  "title": "Fshare",
  "version": "0.1.9",
  "file": "fshare.js",
  "icon": "logo.png",
  "type": "ecmascript",
  "showtimeVersion": "5.0",
  "category": "video"
}
```

The `id` field is used for the release zip name (`<id>-<version>.zip`).  
If missing, the workflow falls back to stripping the `movian-plugin-` prefix from the repository name.

### Logo / icon public URL

```
https://raw.githubusercontent.com/<owner>/<repo>/HEAD/logo.png
```

or

```
https://github.com/<owner>/<repo>/raw/HEAD/logo.png
```

---

## Releasing a plugin (on demand)

Releases are **manual** — nothing runs on every push.

1. Open the plugin repository on GitHub.
2. Go to **Actions → Release → Run workflow**.
3. Choose:
   - **bump** – `patch` / `minor` / `major`
   - **prerelease** – mark the GitHub Release as pre-release (optional)
   - **dry_run** – only print the next version, no commit/tag/release
4. Run the workflow.

The central reusable workflow will:

1. Read current `version` from `plugin.json`
2. Bump it according to the chosen type
3. Commit the change
4. Create an annotated tag (`X.Y.Z`)
5. Build `<plugin-id>-<version>.zip` (excludes `.git`, `.github`, existing zips)
6. Publish a GitHub Release and attach the zip

### Central reusable workflow

Hosted in this organization (or a dedicated actions repo):

```
.github/workflows/release-movian-plugin.yml
```

Each plugin only needs a thin caller (`.github/workflows/release.yml`):

```yaml
name: Release

on:
  workflow_dispatch:
    inputs:
      bump:
        description: "Version bump type"
        required: true
        type: choice
        options: [patch, minor, major]
        default: patch
      prerelease:
        description: "Mark as pre-release?"
        type: boolean
        default: false
      dry_run:
        description: "Dry run (no commit / tag / release)?"
        type: boolean
        default: false

jobs:
  release:
    uses: m-plugs/movian-actions/.github/workflows/release-movian-plugin.yml@main
    with:
      bump: ${{ inputs.bump }}
      prerelease: ${{ inputs.prerelease }}
      dry_run: ${{ inputs.dry_run }}
    secrets: inherit
```

Change `m-plugs/movian-actions` to the real path of the reusable workflow.

---

## Adding a new plugin

1. Create a new repository named `movian-plugin-<id>`.
2. Add the standard files (`plugin.json`, main script, icon, …).
3. Copy the thin caller workflow above into `.github/workflows/release.yml`.
4. Push to the default branch.
5. Run the **Release** workflow once to publish the first version.
6. Add a row for the new plugin in the table at the top of this README.

---

## Plugin feeds (optional)

Movian 6+ supports multiple plugin feeds.  
You can generate a feed JSON from a list of GitHub repos with tools such as [movian-repo](https://github.com/czz/movian-repo):

```text
# repos.txt
/m-plugs/movian-plugin-fshare
/m-plugs/movian-plugin-rap
```

```bash
python build.py -i repos.txt -o repo.json
```

Host `repo.json` (GitHub Pages, raw URL, …) and point Movian at it.

---

## License

Unless otherwise stated in a specific repository, plugins in this organization are provided as-is for personal use with Movian.  
Check each repository’s `LICENSE` (if present) for details.

---

## Links

- [Movian](https://github.com/andoma/movian)
- [Movian documentation & examples](https://github.com/Buksa/movian-docs)
- [Plugin feed generator](https://github.com/czz/movian-repo)
