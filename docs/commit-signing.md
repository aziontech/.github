# Signing commits with an SSH key

The `aziontech` organization requires signed commits on protected branches. Once the rule is enforced, a push or
merge that carries a commit without a verified signature is rejected. The rule is announced before it is enforced.

Setup takes about five minutes and can reuse the SSH key you already push with.

- Commits made in the GitHub web interface (web edits, squash merges) are signed by GitHub. They need nothing.
- Commits already on a protected branch are not checked again. The rule applies to what is pushed or merged from now on.
- SSH signing needs git 2.34 or later (`git --version`).

## Merging pull requests

How a pull request can be merged into a protected branch once the rule is enforced:

| Merge method | Works? | Why |
|---|---|---|
| Squash and merge | Yes, even with unsigned commits on the branch | GitHub creates and signs the squashed commit |
| Create a merge commit | Only if every commit on the branch is signed | The branch's commits land on the protected branch as they are |
| Rebase and merge | No | Rebasing rewrites the commits and GitHub cannot sign them: `Rebase merges cannot be automatically signed by GitHub` |

Repositories that rebase to keep a linear history should squash instead: a squash merge is linear too.

`gh pr merge --squash` can refuse a pull request with unsigned commits, because the CLI reads the pull request as
blocked before asking GitHub. The merge itself is allowed: use the web interface, or the API:

```bash
gh api -X PUT repos/aziontech/<repo>/pulls/<number>/merge -f merge_method=squash
```

## 1. Have an SSH key

The key you already push with works. To check whether one exists:

```bash
ls ~/.ssh/id_ed25519.pub ~/.ssh/id_rsa.pub 2>/dev/null
```

If a file is listed, go to step 2. Otherwise, create an ed25519 key with a passphrase:

```bash
ssh-keygen -t ed25519 -C "you@azion.com"
```

Accept the default path (`~/.ssh/id_ed25519`) and set a passphrase. On macOS, store the passphrase in the Keychain so
you are not asked for it on every commit:

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

To keep a separate key for signing only, create one with `-f ~/.ssh/id_ed25519_signing` and use that path in the
steps below.

## 2. Configure git

Run once per machine:

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
git config --global tag.gpgsign true
```

Check that git's email is a verified email on your GitHub account. Without that, the commit shows as Unverified even
with a valid signature.

```bash
git config --global user.email   # must be an email verified on your GitHub account
```

Optional, so that `git log --show-signature` can verify your signatures locally:

```bash
echo "$(git config --global user.email) $(cat ~/.ssh/id_ed25519.pub)" >> ~/.ssh/allowed_signers
git config --global gpg.ssh.allowedSignersFile ~/.ssh/allowed_signers
```

## 3. Add the key to GitHub as a Signing Key

This is the step most often missed. The key you push with is registered as an Authentication Key, which does not count
for signatures. The same key has to be added again, as a Signing Key.

1. Copy the public key: `pbcopy < ~/.ssh/id_ed25519.pub` (on Linux, `cat` it and copy).
2. Open [github.com/settings/ssh/new](https://github.com/settings/ssh/new).
3. Under **Key type**, choose **Signing Key**.
4. Give it a title (for example `MacBook – signing`), paste the key and save.

The same with the GitHub CLI:

```bash
gh ssh-key add ~/.ssh/id_ed25519.pub --type signing --title "MacBook - signing"
```

If `gh` asks for permission, run `gh auth refresh -s admin:ssh_signing_key` first.

## 4. Verify

Make a commit and check it locally:

```bash
git commit --allow-empty -m "test: signed commit"
git log --show-signature -1
```

With `allowed_signers` configured, the output shows `Good "git" signature for you@azion.com`. Without it, it shows
`gpg.ssh.allowedSignersFile needs to be configured`, and the commit is signed all the same.

After pushing to a test branch, the commit shows the **Verified** badge in the pull request or the repository's commit
list. From the command line:

```bash
gh api repos/aziontech/<repo>/commits/<sha> --jq '.commit.verification | {verified, reason}'
```

Expect `verified: true` and `reason: valid`. Remember to drop the test commit.

## Special cases

**Several machines.** Repeat steps 2 and 3 on each machine, with one Signing Key per machine. An account can hold as
many as needed.

**Coder, devcontainers and remote SSH.** On the remote machine, let git sign through the forwarded ssh-agent instead of
copying the private key there. On the remote machine, run:

```bash
git config --global gpg.format ssh
git config --global user.signingkey "key::$(ssh-add -L | head -1)"
git config --global commit.gpgsign true
```

This needs a forwarded agent (`ssh -A`, or `ForwardAgent yes` in `~/.ssh/config`). VS Code Remote and devcontainers
forward it on their own.

**1Password.** If the key lives in 1Password, use its signing program:

```bash
git config --global gpg.ssh.program "/Applications/1Password.app/Contents/MacOS/op-ssh-sign"
```

The rest is the same, with `user.signingkey` set to the public key copied from 1Password.

**IDEs.** VS Code, JetBrains IDEs and GitHub Desktop read git's global configuration. Nothing to set in them.

**Unsigned commits already on your branch.** Sign them again and force-push. Only on your own branches, never on
shared ones.

```bash
git rebase --exec 'git commit --amend --no-edit -S' origin/<default-branch>
git push --force-with-lease
```

**Automations that commit.** CI jobs that run `git commit` and `git push` (version bumps, changelogs, generated code)
are rejected on protected branches. Either commit through the GitHub API with `GITHUB_TOKEN`, which GitHub signs, or
give the bot a signing key of its own.

## Common problems

The `reason` field from the verification command (step 4) usually names the cause.

| Symptom | Cause | Fix |
|---|---|---|
| Unverified, `reason: unknown_key` | Key registered only as an Authentication Key, or not at all | Add it as a Signing Key (step 3) |
| Unverified, `reason: unverified_email` or `bad_email` | git's `user.email` is not a verified email on the account | Fix `user.email`, or verify the email on GitHub |
| Commit with no badge at all | `commit.gpgsign` is off in that repository or on that machine | Run step 2; check with `git config commit.gpgsign` |
| `unsupported value for gpg.format: ssh` | git older than 2.34 | Upgrade git (`brew upgrade git`) |
| `agent refused operation`, or a passphrase prompt on every commit | Key not loaded in the ssh-agent | `ssh-add --apple-use-keychain ~/.ssh/id_ed25519` |
| Push rejected with `Commits must have verified signatures` | An unsigned commit on the branch | Sign the commits again (Special cases) |
| Merge refused with `Commits must have verified signatures` | Merge commit with unsigned commits on the branch | Squash and merge, or sign the commits again |
| Merge refused with `Rebase merges cannot be automatically signed by GitHub` | Rebase and merge on a protected branch | Squash and merge (Merging pull requests) |

Azion engineers with questions not answered here: ask `@aziontech/team-delivery-engineering`.
