# CI/CD - Continuous Integration & Deployment

build & test using github registry; deploy to nomad clusters, using
[GitHub Actions reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

([workflows syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions))

---

# Example usage & setup
1. Copy the yaml below into your github repo with a `Dockerfile` - (any name, `cicd.yml`, etc.) in a subdir from the top: `.github/workflows/`:

```yaml
on: push
jobs:
  cicd:
    uses: internetarchive/cicd/.github/workflows/cicd.yml@main
```

2. If you want to deploy to a nomad cluster add a `NOMAD_TOKEN` secret:

```yml
on: push
jobs:
  cicd:
    uses: internetarchive/cicd/.github/workflows/cicd.yml@main
    secrets:
      NOMAD_TOKEN: ${{ secrets.NOMAD_TOKEN }}
```

⭐ **For each repo you use this with**, _add a_ ⭐
[GitHub Secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
with name: `NOMAD_TOKEN`, getting the value from a nomad cluster admin (for archive.org: tracey, brenton, robK, etc.)

If not an archive.org repo, update these two arguments to the nomad cluster wildcard DNS domain and API URL you can add `BASE_DOMAIN` and `NOMAD_ADDR` to your yaml:
```yaml
on: push
jobs:
  cicd:
    uses: internetarchive/cicd/.github/workflows/cicd.yml@main
    with:
      BASE_DOMAIN: 'example.com'
      NOMAD_ADDR: 'https://nomad.example.com:4646'
    secrets:
      NOMAD_TOKEN: ${{ secrets.NOMAD_TOKEN }}
```

You can then find your `Actions` tab in your repo and you should see the build, (optional test), and deploy fire off.

The logs will show you the webapp URL for your deployed project.

---

## Optional production cluster (archive.org repos only)
To deploy to the archive.org "high availability" production cluster, you simply need to:
- Add another
[GitHub Secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
named `NOMAD_TOKEN_PROD`,
getting the value from a nomad cluster admin (for archive.org: tracey, robK, brenton etc.)
- Add this to your yaml (above):

```yaml
on: push
jobs:
  cicd:
    uses: internetarchive/cicd/.github/workflows/cicd.yml@main
    secrets:
      NOMAD_TOKEN_PROD: ${{ secrets.NOMAD_TOKEN_PROD }}
```
- push a branch named `production` for your repo

---

## Customize your deploy
You can send various `NOMAD_VAR_*` variables into the [deploy] phase, options listed here:
- [.github/workflows/cicd.yml](.github/workflows/cicd.yml)

You can see explanations for the various options here:
- https://gitlab.com/internetarchive/nomad#customizing
- NOTE: while the snippet examples are gitlab repo-centric, mentally substitute
the documentation there which says `variables:` to be `with:` like below example:

```yaml
on: push
jobs:
  cicd:
    uses: internetarchive/cicd/.github/workflows/cicd.yml@main
    with:
      NOMAD_VAR_MEMORY: 1000
    secrets:
      NOMAD_TOKEN: ${{ secrets.NOMAD_TOKEN }}
```

---

## Multi arch builds (eg: for mac ARM)
If you want to build for linux/x86 _and_ mac ARM you can add `PLATFORMS`:

```yaml
on: push
jobs:
  cicd:
    uses: internetarchive/cicd/.github/workflows/cicd.yml@main
    with:
      PLATFORMS: 'linux/amd64, linux/arm64'
```


## Want a more custom pipeline?
You can setup arbitrary jobs, custom tests, and more.

See this nice example which splits out the same [build] and [deploy] steps used normally, into explicit jobs, dependent on other test jobs, etc.

- https://github.com/internetarchive/hello-js
- https://github.com/internetarchive/hello-js/blob/main/.github/workflows/cicd.yml

---

## Interact with / debug your deploy
You can see more information on how to `ssh` in to your running deployment, inspect logs, get detailed status information, and even "hot copy" updated files into a running deploy (and more) at:
- https://gitlab.com/internetarchive/nomad/-/blob/master/README.md#laptop-access
- https://gitlab.com/internetarchive/nomad/-/blob/master/README.md#inspect-poke-around

---

## Interesting in linting code?
Check out https://github.com/internetarchive/dyno
