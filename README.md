# gha-cherry-pick-release-branch

This action supports a Release Flow branching strategy.  After a new release tag is created, this will create a new release branch based on the first commit of the default branch, then create a cherry-pick branch from the latest release tag and a pull request to the new release branch.

This is designed to work with [dustindortch/gha-generate-semver@v1](/dustindortch/gha-generate-semver)

## Inputs

* prefix
  * description: 'Version tag prefix (default: v)'
  * required: false
* branch_prefix
  * description: 'Branch prefix (default: release)'
  * required: false
* tag
  * description: 'Tag to cherry-pick'
  * required: true
* default_branch
  * description: 'Default branch'
  * required: false

## Outputs

None

## Usage

```yaml
    steps:
      - uses: actions/checkout@v4

      - name: Generate next version
        id: next-version
        uses: dustindortch/gha-generate-semver@v1

      - name: Tag release
        id: tag-release
        run: |
          release=${{ steps.next-version.outputs.new-version }}
          username="$(git --no-pager log --format=format:'%an' -n 1)"
          email=$(git --no-pager log --format=format:'%ae' -n 1)
          git config --global user.name "${username}"
          git config --global user.email "${email}"
          git tag ${release} -m "Release ${release}"
          git push --tags

      - name: Cherry-pick release
        id: cherry-pick-release
        uses: dustindortch/gha-cherry-pick-release-branch@v1
        with:
          tag: ${{ steps.next-version.outputs.new-version }}
```
