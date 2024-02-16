# CI/CD - Continuous Integration & Deployment

build &amp; test using github registry; deploy to nomad clusters

---

# Example usage & setup
1. Copy the yaml below into your github repo with a `Dockerfile` - (any name, `cicd.yml`, etc.) in a subdir from the top: `.github/workflows/`:

```yaml
name: CICD
on: push

jobs:
  cicd:
    # https://github.com/internetarchive/cicd
    uses: internetarchive/cicd/.github/workflows/cicd.yml@main
    secrets:
      NOMAD_TOKEN: ${{ secrets.NOMAD_TOKEN }}
      REGISTRY_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
This uses
[GitHub Actions reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

2. ⭐ **For each repo you use this with**, _add a_ ⭐
[GitHub Secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
with name: `NOMAD_TOKEN`, getting the value from a nomad cluster admin (for archive.org: tracey, matt mcneil, brenton, etc.)

If not an archive.org repo, update these two arguments to the nomad cluster wildcard DNS domain and API URL you use:
```yaml
    with:
      BASE_DOMAIN: 'example.com'
      NOMAD_ADDR: 'https://nomad.example.com:4646'
```

( `REGISTRY_TOKEN` is automatically taken care of for you )

You can then find your `Actions` tab in your repo and you should see the build, (optional test), and deploy fire off.

The logs will show you the webapp URL for your deployed project.

---

## Optional production cluster (archive.org repos only)
To deploy to the archive.org "high availability" production cluster, you simply need to:
- Add another
[GitHub Secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
named `NOMAD_TOKEN_PROD`,
getting the value from a nomad cluster admin (for archive.org: tracey, matt mcneil, brenton, etc.)
- Add this to your `jobs.cicd.secrets` (above):
```yaml
      NOMAD_TOKEN_PROD: ${{ secrets.NOMAD_TOKEN_PROD }}
```
- push a branch named `production` for your repo

---

## Customize your deploy
You can send various `NOMAD_VAR_*` variables into the [deploy] phase, options listed here:
- [action.yml](action.yml)

You can see explanations for the various options here:
- https://gitlab.com/internetarchive/nomad#customizing
- NOTE: while the snippet examples are gitlab repo-centric, mentally substitute
the documentation there which says `variables:` to be `with:` (see 'Example Usage & Setup' above)

---

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
