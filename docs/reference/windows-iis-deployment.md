# Windows IIS Deployment Guide (GitHub Post)

This guide explains how to deploy `LinkOn.Modeler.Web` on Windows + IIS.

## 1) Prerequisites

- Windows Server or Windows 10/11
- IIS installed
- Node.js + npm installed
- (Recommended) IIS URL Rewrite module installed

## 2) Build Options

Run from repository root:

```powershell
cd E:\Temp\LinkOn.Modeler\LinkOn.Modeler.Web
```

### Option A: Relative asset path (`./`) for flexible sub-path hosting

```powershell
npm run build:iis
```

- Output directory: `dist-publish`
- Suitable when app can be mounted under various IIS paths

### Option B: Fixed `/Modeler/` base path

```powershell
npm run build:iis:modeler
```

- Output directory: `dist-publish`
- Use when IIS app path is fixed to `/Modeler`

## 3) Publish to IIS Physical Path

### Build + Publish

```powershell
npm run publish:iis -- -DestinationPath "C:\inetpub\wwwroot\Modeler"
```

### Publish only (skip build)

```powershell
npm run publish:iis -- -DestinationPath "C:\inetpub\wwwroot\Modeler" -SkipBuild
```

### Publish with clean target

```powershell
npm run publish:iis -- -DestinationPath "C:\inetpub\wwwroot\Modeler" -Clean
```

### Publish with root landing page (`/` -> shortcut to `./Modeler/`)

```powershell
npm run publish:iis -- -DestinationPath "C:\inetpub\wwwroot\Modeler" -IncludeRootIndex -ModelerPath "./Modeler"
```

## 4) IIS Configuration Checklist

- IIS Site or Application points to deployment path (for example `C:\inetpub\wwwroot\Modeler`)
- `web.config` exists in deployed folder (included automatically from `public/web.config`)
- `web.config` provides:
  - MIME mapping for `.qmf`, `.qml`, `.qdg`
  - SPA fallback rewrite to `index.html`
- If deep link routing returns `404`, install IIS URL Rewrite module

## 5) Verification

After deployment:

1. Open `http://<server>/Modeler/`
2. Confirm static assets (JS/CSS/icons) load without 404
3. Open a `.qmf` file
4. Open a `.qml` or `.qdg` file in Log Analyzer

## 6) Typical Troubleshooting

- `404` for `index-*.css` or `favicon.ico`:
  - Check app base path and IIS virtual path alignment (`/Modeler`)
- Icons not visible:
  - Ensure full `dist-publish` content is copied (including `icons` assets)
- Route refresh 404:
  - Verify URL Rewrite is installed and `web.config` rewrite rule is active

