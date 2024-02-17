
- Extract some info in one step and pass it to another (uses a deprecated `set-output` call here):
https://github.blog/changelog/2022-10-11-github-actions-deprecating-save-state-and-set-output-commands/
```yaml
    steps:
      - name: Extract branch name
        shell: bash
        run: echo "##[set-output name=branch;]$(echo ${GITHUB_REF#refs/heads/})"
        id: extract_branch

      - name: Login to registry
        uses: docker/login-action@v3
        with:
          tags: ghcr.io/${{ github.repository }}:${{ steps.extract_branch.outputs.branch }}
```
