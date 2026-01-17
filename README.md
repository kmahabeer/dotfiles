# Readme

This repository contains my personal dotfiles and system configuration, version-controlled with Git for reproducibility and portability.

## Repository Contents

- Shell configuration (zsh, bash)
- Git configuration
- Prompt configuration (Starship)
- Tool-specific configs
- Bootstrap scripts for symlinking

All dotfiles live inside this repository and are exposed to the system via symlinks.

## Getting Started

```bash
git clone git@github.com:<user>/dotfiles.git ~/.dotfiles
cd ~/.dotfiles
./scripts/link.sh
```

## How Dotfiles Are Managed

Dotfiles are **not** tracked directly from `$HOME`.

Instead:

- Files live in `~/.dotfiles`
- `$HOME` contains symlinks
- Git tracks only intentional configuration

For details, see:

- `GUIDE.md`

## Safety

Sensitive files (SSH keys, tokens, secrets) are intentionally excluded and should never be committed.

## Notes

This repository is opinionated and tailored to my workflow. Use it as a reference, not a drop-in solution.
