---
theme: gaia
_class: lead
paginate: true
backgroundColor: #fff
backgroundImage: url('https://marp.app/assets/hero-background.svg')
---

# Building a Simple Portfolio Website with Blazor

with the Object-Oriented Programming principle

---

# Create a New Fluent WASM Project

Install dependencies:

- `BlazorWasmPreRendering.Build`
- `PublishSPAforGitHubPages.Build`

---

# Publishing to GitHub Pages

First, create `<username>.github.io` repo.

---

## GitHub Actions

`.github/workflows/deploy.yml`

```yaml
name: Deploy Blazor to GitHub Pages
on:
  push:
    branches: [ main ] # Or master / anything else
  
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    timeout-minutes: 60
    steps:

    # Checkout the code
    - uses: actions/checkout@v4

    # Install .NET SDK
    - name: Setup .NET SDK
      uses: actions/setup-dotnet@v4
      with:
        dotnet-version: 9.0.x
    - name: Install .NET WebAssembly Tools
      run: dotnet workload install wasm-tools

    - name: publish website
      run: dotnet publish src/BlazorApps/BlazorApps.csproj --configuration Release -p:GHPages=true

    - name: Upload all published files
      uses: actions/upload-pages-artifact@v3
      with:
        path: src/BlazorApps/bin/Release/net9.0/publish/wwwroot

  # Deployment job
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
