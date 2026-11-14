---
name: forking
description: "Use when forking or cloning Git repositories, or managing existing Git repository clones."
---

# Forking

Set up GitHub repositories consistently for fork-based GitHub Flow. Treat the source repository as `upstream`, the user's fork as `origin`, and the lowercase source owner as the local organization directory.

## Workflow

1. Resolve the source repository to its canonically cased `<upstream-owner>/<repo>`.
2. Derive `<owner-dir>` by lowercasing `<upstream-owner>`. Use `<owner-dir>` only for local filesystem paths; preserve the canonical owner in GitHub repository names and remote URLs.
3. Confirm `gh` is authenticated and determine the authenticated account with `gh api user --jq .login`.
4. Set the target checkout to `~/Projects/<owner-dir>/<repo>`.
5. Inspect before changing anything:
   - Check whether the target directory already exists.
   - Check whether `<account>/<repo>` already exists and is a fork of the intended source.
   - Never overwrite an existing directory or silently repurpose a fork of another repository.
6. Create the fork with `gh repo fork <upstream-owner>/<repo>` without the `--clone` flag, or reuse the existing matching fork.
7. Create `~/Projects/<owner-dir>` and clone the fork into the target with `gh repo clone <account>/<repo> <target>`. Let the user's `gh` Git protocol setting choose SSH or HTTPS.
8. In the checkout, add a remote named `upstream` pointing to the original repository. Match the protocol used by `origin`. If `upstream` already exists, verify it and correct its URL only when necessary.
9. Run `gh repo set-default <upstream-owner>/<repo>` from the checkout so issue, pull-request, and other ambiguous `gh` commands target the original repository by default.
10. Verify the result with `git remote -v` and `gh repo set-default --view`.

## Result

Leave the checkout in this shape:

```text
~/Projects/<owner-dir>/<repo>
  origin    -> <account>/<repo>
  upstream  -> <upstream-owner>/<repo>
  gh default repository -> <upstream-owner>/<repo>
```

Use `origin` for branches and pushes. Use `upstream` to fetch changes from the original project.

## Exceptions

- Follow an explicit request to use a different owner, directory, protocol, remote name, or non-fork clone.
- If the source is not hosted on GitHub, preserve the directory convention where practical but do not invent a GitHub fork workflow.
- If authentication, fork ownership, an occupied target, or repository identity is ambiguous, stop before creating or overwriting anything and ask for the missing decision.
