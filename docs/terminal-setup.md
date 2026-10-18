---
title: Outils et configuration du terminal
---

# Outils et configuration du terminal

Récapitulatif du setup terminal sur Mac (clavier AZERTY) : ce qui est installé, à quoi ça sert, et la configuration nécessaire. Les raccourcis sont dans la page [Raccourcis terminal](raccourcis-terminal.md).

## Vue d'ensemble

| Outil | Rôle |
|---|---|
| Ghostty | Terminal (onglets et splits natifs) |
| JetBrainsMono Nerd Font | Police avec icônes, requise par Starship et eza |
| zsh + Oh My Zsh | Shell et framework de plugins |
| Starship | Prompt dynamique (git, Kubernetes, Node...) |
| zoxide | Navigation rapide entre dossiers (`z`) |
| fzf | Recherche floue (historique, fichiers) |
| ripgrep, fd | Recherche rapide dans le contenu et les noms de fichiers |
| bat | `cat` avec coloration syntaxique |
| eza | `ls` moderne avec icônes et statut git |
| lazygit | Interface git dans le terminal |
| k9s | Interface Kubernetes dans le terminal |
| Neovim + LazyVim | Éditeur modal préconfiguré |
| Catppuccin | Thème de couleurs unifié |

!!! note "Choix écartés"
    **Zellij** a été testé puis abandonné : les splits et onglets de Ghostty suffisent en local. **tmux** reste utile uniquement pour les sessions persistantes sur des serveurs distants (SSH).

## Installation

```bash
brew install --cask ghostty
brew install --cask font-jetbrains-mono-nerd-font
brew install starship fzf zoxide ripgrep fd bat eza lazygit k9s neovim
```

Optionnel : `zsh-autosuggestions` et `zsh-syntax-highlighting` (`brew install`), et `gh` pour GitHub en ligne de commande.

## Ghostty

Terminal rapide, rendu natif macOS, configuration en quelques lignes. Gère onglets (`Cmd+T`) et splits (`Cmd+D`) sans multiplexer.

Fichier `~/.config/ghostty/config` :

```
font-family = JetBrainsMono Nerd Font
font-size = 14
theme = catppuccin-mocha
window-padding-x = 8
window-padding-y = 8
shell-integration = zsh
```

- Recharger la config : `Cmd+Shift+,`, ou quitter complètement Ghostty (`Cmd+Q`) si besoin.
- Si le nom de la police n'est pas reconnu : `ghostty +list-fonts | grep -i jetbrains`.
- Si la config ne semble pas prise en compte, vérifier qu'il n'existe pas un second fichier dans `~/Library/Application Support/com.mitchellh.ghostty/config`.

## zsh, Oh My Zsh, Starship

### Ordre des lignes dans `~/.zshrc`

L'ordre compte : Oh My Zsh d'abord, puis les initialisations.

```bash
ZSH_THEME=""            # Starship gère le prompt, pas de thème Oh My Zsh
plugins=(git)           # ne pas mettre le plugin "z" (conflit avec zoxide)

source $ZSH/oh-my-zsh.sh

eval "$(starship init zsh)"
eval "$(zoxide init zsh)"
source <(fzf --zsh)
```

!!! warning "Thème et Starship"
    Garder un thème Oh My Zsh en même temps que Starship est inutile : Starship écrase le prompt mais le thème continue de se charger et peut laisser des restes (`RPROMPT`, hooks). Avec `ZSH_THEME=""`, Starship est seul maître du prompt.

### Alias

```bash
alias ls="eza --icons=auto"
alias ll="eza -l --icons=auto --git"
alias la="eza -la --icons=auto --git"
alias lt="eza --tree --level=2 --icons=auto"
alias cat="bat"
alias lg="lazygit"
alias k="kubectl"
```

!!! warning "eza et `--icons`"
    Dans les versions récentes d'eza, `--icons` accepte une valeur optionnelle (`always`, `auto`, `never`) et avale l'argument suivant. Utiliser toujours `--icons=auto` avec le signe égal, sinon `ls mon/dossier` provoque l'erreur `invalid value ... for '--icons'`.

### Starship

Prompt qui affiche les informations de contexte : branche git, version de Node dans un projet Node, durée des commandes lentes, couleur du `❯` selon le succès de la dernière commande, contexte Kubernetes actif.

Fichier `~/.config/starship.toml` :

```toml
palette = "catppuccin_mocha"

[palettes.catppuccin_mocha]
rosewater = "#f5e0dc"
red = "#f38ba8"
green = "#a6e3a1"
yellow = "#f9e2af"
blue = "#89b4fa"
mauve = "#cba6f7"
peach = "#fab387"
text = "#cdd6f4"

[kubernetes]
disabled = false

[nodejs]
disabled = true

[package]
disabled = true
```

- Le module Kubernetes affiche le contexte actif en permanence, utile pour ne pas lancer une commande sur le mauvais cluster.
- Les icônes manquantes (carré ou vide avant `v21.5.0` par exemple) signalent que la Nerd Font n'est pas utilisée par le terminal.

### zoxide

Remplace les `cd` longs : `z nom` saute dans le dossier le plus fréquent ou récent correspondant. Sa base est vide à l'installation, il faut d'abord passer quelques fois par les dossiers avec `cd`.

Vérification : `type z` doit indiquer une fonction shell.

### fzf, ripgrep, fd, bat

- **fzf** : `Ctrl+R` ouvre un historique cherchable, et il sert de moteur de sélection dans les fonctions ci-dessous.
- **ripgrep** (`rg`) et **fd** : recherche rapide dans le contenu et les noms de fichiers, aussi utilisés par les pickers de LazyVim.
- **bat** : le thème Catppuccin n'est pas fourni par défaut. Liste des thèmes disponibles : `bat --list-themes`.

Couleurs fzf Catppuccin (dans `~/.zshrc`) :

```bash
export FZF_DEFAULT_OPTS=" \
--color=bg+:#313244,bg:#1e1e2e,spinner:#f5e0dc,hl:#f38ba8 \
--color=fg:#cdd6f4,header:#f38ba8,info:#cba6f7,pointer:#f5e0dc \
--color=marker:#b4befe,fg+:#cdd6f4,prompt:#cba6f7,hl+:#f38ba8"
```

## Git : lazygit et worktrees

**lazygit** (`lg`) est une interface TUI qui évite la plupart des commandes git à retenir. Il reprend les couleurs ANSI du terminal, donc Catppuccin automatiquement. Il dispose aussi d'un onglet Worktrees.

Un **worktree** est un second dossier de travail lié au même dépôt, avec sa propre branche : plus besoin de `stash` ou de re-clone pour travailler sur deux branches en parallèle. Convention : les ranger à côté du dépôt (`~/dev/mon-repo`, `~/dev/mon-repo-fix-login`).

Fonctions à ajouter dans `~/.zshrc` :

```bash
# wta <branche> [base] : crée le worktree à côté du repo et s'y place
wta() {
  local branch=$1 base=${2:-main}
  local root=$(git rev-parse --show-toplevel) || return
  local dir="${root}-${branch//\//-}"
  if git show-ref --verify --quiet "refs/heads/$branch"; then
    git worktree add "$dir" "$branch" && cd "$dir"
  else
    git worktree add -b "$branch" "$dir" "$base" && cd "$dir"
  fi
}

# wtc : choisir un worktree avec fzf et y aller
wtc() {
  local dir=$(git worktree list | fzf --height 40% | awk '{print $1}')
  [ -n "$dir" ] && cd "$dir"
}

# wtr : supprimer un worktree (hors principal) avec fzf
wtr() {
  local dir=$(git worktree list | tail -n +2 | fzf --height 40% | awk '{print $1}')
  [ -n "$dir" ] && git worktree remove "$dir"
}
```

Points d'attention :

- Une branche ne peut être checkout que dans un seul worktree à la fois.
- `node_modules`, `.env` et caches ne sont pas partagés entre worktrees.
- Le contexte `kubectl` est global au shell, pas lié au worktree.
- Après suppression manuelle d'un dossier : `git worktree prune`.
- Si ce workflow devient répétitif, l'outil **Worktrunk** (`wt`) ajoute des hooks de création (installation des dépendances, copie du `.env`). Vérifier son README avant installation.

## Neovim avec LazyVim

LazyVim est une distribution Neovim préconfigurée (LSP, recherche floue, explorer, thème). Le `<leader>` est la touche `Espace`.

### Thème Catppuccin

Fichier `~/.config/nvim/lua/plugins/catppuccin.lua` :

```lua
`return {
  { "catppuccin/nvim", name = "catppuccin", lazy = false, priority = 1000 },
  { "LazyVim/LazyVim", opts = { colorscheme = "catppuccin-mocha" } },
}
```

Vérification : `:colorscheme` doit afficher `catppuccin-mocha`, et `:Lazy sync` télécharge le plugin si besoin.

### Afficher les fichiers cachés (`.github`, `.env`)

Par défaut, l'explorer et la recherche de fichiers masquent les fichiers commençant par un point. Bascule à la volée : `H` dans l'explorer.

Affichage permanent, selon l'explorer utilisé. Avec **snacks.nvim** (versions récentes), fichier `~/.config/nvim/lua/plugins/explorer.lua` :

```lua
return {
  "folke/snacks.nvim",
  opts = {
    picker = {
      sources = {
        explorer = { hidden = true },
        files = { hidden = true },
      },
    },
  },
}
```


Les noms d'options peuvent varier légèrement selon la version.

### Problèmes courants

| Symptôme | Cause et solution |
|---|---|
| Nvim n'occupe que la moitié de l'écran | Une autre fenêtre (terminal intégré, quickfix, aide) est réduite au minimum. `Ctrl+w` puis `_` redonne l'espace, `Ctrl+w` puis `j` puis `Espace wd` ferme la fenêtre du dessous |
| Raccourcis shell (`Option+←`) inactifs dans l'éditeur | Ce sont des raccourcis zsh, pas nvim. Utiliser `w`, `b`, `0`, `$` |

## Optionnel

- **Karabiner-Elements** : transformer Caps Lock en `Esc` (appui seul) et `Ctrl` (maintenu), très utile avec vim sur un clavier Mac.
- **atuin** : historique shell synchronisé et cherchable.
- **tmux** : uniquement pour les sessions persistantes sur serveurs distants.

## Déploiement de cette documentation

La documentation est publiée avec MkDocs Material sur GitHub Pages via une GitHub Action.

- `requirements.txt` fige les versions de `mkdocs` et `mkdocs-material`.
- `mkdocs.yml` active les extensions `admonition`, `tables` et `pymdownx.superfences`.
- Le workflow `.github/workflows/deploy.yml` déploie à chaque `git push` sur `main`.
- GitHub Pages doit être réglé sur **Settings, Pages, Source : GitHub Actions**.
- Le bloc `on:` du workflow doit être correctement indenté (2 espaces), sinon GitHub renvoie l'erreur `No event triggers defined in on`.
- Un site GitHub Pages est public : ne rien y mettre de sensible.
