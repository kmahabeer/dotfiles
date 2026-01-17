# Version Controlling Dotfiles

This guide describes a clean, reproducible way to version-control dotfiles using Git **without** turning your entire home directory into a repository.

The approach is:

- keep dotfiles in a dedicated Git repo
- expose them to the system via symlinks
- avoid tracking unrelated or sensitive files

## Goals

- Track configuration changes over time
- Sync dotfiles across machines
- Avoid polluting `$HOME` with Git metadata
- Keep secrets out of version control
- Make onboarding a new machine predictable

## Recommended Structure

```txt
~/.dotfiles/
├── bash/
│   └── .bashrc
├── zsh/
│   └── .zshrc
├── git/
│   └── .gitconfig
├── starship/
│   └── starship.toml
├── scripts/
│   └── link.sh
└── README.md
````

Dotfiles live **inside the repo**.  

Your home directory only contains symlinks.

## Step 1 — Create the Dotfiles Repository

```bash
mkdir ~/.dotfiles
cd ~/.dotfiles
git init
````

Optionally create subdirectories to keep things organized by tool or shell.

## Step 2 — Move Existing Dotfiles Into the Repo

Move each config file from `$HOME` into the repo.

```bash
mv ~/.zshrc ~/.dotfiles/zsh/.zshrc
mv ~/.gitconfig ~/.dotfiles/git/.gitconfig
```

Do **not** delete functionality yet—symlinks come next.

## Step 3 — Create Symlinks Back to `$HOME`

Symlink each file from the repo to where the program expects it.

```bash
ln -s ~/.dotfiles/zsh/.zshrc ~/.zshrc
ln -s ~/.dotfiles/git/.gitconfig ~/.gitconfig
```

From the system’s perspective, nothing changes.

From Git’s perspective, everything is now centralized.

## Step 4 — Commit and Push

```bash
git add .
git commit -m "Initial dotfiles"
git remote add origin git@github.com:<user>/dotfiles.git
git push -u origin main
```

Your dotfiles are now version-controlled and backed up.

## Step 5 — Add a Bootstrap Script (Recommended)

Create a script to recreate symlinks automatically on new machines.

`scripts/link.sh`

```bash
#!/usr/bin/env bash
set -e

ln -sf ~/.dotfiles/zsh/.zshrc ~/.zshrc
ln -sf ~/.dotfiles/git/.gitconfig ~/.gitconfig
ln -sf ~/.dotfiles/starship/starship.toml ~/.config/starship.toml
```

Make it executable:

```bash
chmod +x scripts/link.sh
```

This avoids manual setup errors.

## Step 6 — Ignore Sensitive or Machine-Specific Files

Add a `.gitignore` to prevent leaking secrets:

```gitignore
.ssh/
.gnupg/
.env
.env.*
```

Never commit:

- SSH keys
- GPG keys
- API tokens
- Password files

## Step 7 — Set Up a New Machine

On a fresh system:

```bash
git clone git@github.com:<user>/dotfiles.git ~/.dotfiles
cd ~/.dotfiles
./scripts/link.sh
```

Your environment is restored.

## Why Not Make `$HOME` a Git Repo?

Technically possible. Practically painful.

Problems:

- Massive `.gitignore`
- Easy to commit junk accidentally
- Breaks tools that expect `$HOME` to be non-git
- Hard to reason about scope

Keeping a **dedicated dotfiles repo** avoids all of this.

## Alternative: Bare Git Repository (Advanced)

Some users prefer a *bare repo* with `$HOME` as the working tree:

```bash
git init --bare ~/.dotfiles
alias dotfiles='git --git-dir=$HOME/.dotfiles --work-tree=$HOME'
```

Pros:

- No symlinks
- Files stay in place

Cons:

- Easy to shoot yourself in the foot
- Harder to reason about
- Less explicit structure

For most setups, **symlinks + normal repo** is clearer and safer.

## Summary

- Do **not** version-control `$HOME`
- Store dotfiles in `~/.dotfiles`
- Use symlinks to expose them
- Track changes with Git
- Automate setup with a script
