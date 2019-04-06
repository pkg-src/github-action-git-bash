# [github-action-git-bash](https://github.com/fnkr/github-action-git-bash)

GitHub Action to run a command or script in a Git-ready environment.
Possible use cases include push mirroring, committing and pushing changes,
merging branches, or creating tags from a CI pipeline.

## Secrets

- `GITHUB_TOKEN` — **Optional.** If set, the action will set Git's `user.name` and `user.email` options to the actors name and email. This is useful when creating commits.
- `SSH_KEY` — **Optional.** If set, the action will start an SSH agent and insert the key. This is useful for authenticating with remote repositories (e.g. for push mirroring). The secret must contain an unencrypted private key without trailing line feed.

## Variables

- `SSH_KEY_VAR` — **Optional.** Name of the `SSH_KEY` secret. This can be useful if the action is used multiple times in a workflow.

## Usage example

```hcl
workflow "On push" {
  on = "push"
  resolves = ["Update mirror"]
}

action "Update mirror" {
  uses = "fnkr/github-action-git-bash@v1"
  secrets = ["GITHUB_TOKEN", "SSH_KEY"]
  args = ["bash", "-c", "{ [ \"$(git rev-parse --abbrev-ref HEAD)\" = \"master\" ] || exit 78; } && rm -rf .github && git add -A .github && git commit -m \"Remove .github\" && git remote set-url origin git@github.com:GITHUB/PROJECT.git && git push --force origin master"]
}
```
