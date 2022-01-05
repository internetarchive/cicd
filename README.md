# CI/CD - Continuous Integration & Deployment

build &amp; test using github registry; deploy to nomad clusters

# Example usage & setup
Copy this into your github repo (any name, `cicd.yml`, etc.) in a subdir from the top: `.github/workflows/`:
```yaml
name: CICD
on: push
jobs:
  cicd:
    runs-on: ubuntu-latest
    permissions:
       contents: read
       packages: write
       id-token: write
    steps:
      # https://github.com/internetarchive/cicd
      - uses: internetarchive/cicd@v1
        with:
          BASE_DOMAIN: 'dev.archive.org'
          NOMAD_ADDR: 'https://nom.us.archive.org:4646'
          NOMAD_TOKEN: ${{ secrets.NOMAD_TOKEN }}
          REGISTRY_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

⭐ **Each repo you use this with**, _add a_ 
[GitHub Secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
with name: `NOMAD_TOKEN`, getting the value from an archive.org admin (like tracey, matt mcneil, brenton, etc.)

If not an archive.org repo, update these two arguments to the nomad cluster wildcard DNS domain and API URL you use:
```
         BASE_DOMAIN: 'example.com'
         NOMAD_ADDR: 'https://nomad.example.com:4646'
```

( `REGISTRY_TOKEN` is automatically taken care of for you )

You can then find your `Actions` tab in your repo and you should see the build, (optional test), and deploy fire off.

The logs will show you the webapp URL for your deployed project.


## Optional production cluster (archive.org repos only):
To deploy to the archive.org "high availability" production cluster, you simply need to:
- Add another 
[GitHub Secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
named `NOMAD_PROD`, 
getting the value from an archive.org admin (like tracey, matt mcneil, brenton, etc.)
- Add this to your `jobs.steps.with` (above):
```yaml
          NOMAD_PROD: ${{ secrets.NOMAD_PROD }}
```
- push a branch named `production` for your repo
