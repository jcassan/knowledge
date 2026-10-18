---
title: Raccourcis terminal (Ghostty, zsh, LazyVim)
---

# Raccourcis terminal

Aide-mémoire pour le setup **Ghostty + zsh + LazyVim** sur un **clavier AZERTY Mac**.

!!! tip "Leader nvim"
    Dans LazyVim, `Espace` est la touche `<leader>`. En cas de doute, appuie sur `Espace` et attends une seconde : which-key affiche tous les raccourcis disponibles.

## Le minimum à retenir

| Action | Raccourci |
|---|---|
| Ouvrir un fichier | `Espace ff` |
| Naviguer entre fichiers ouverts | `Shift+h` / `Shift+l` |
| Fermer un fichier | `Espace bd` |
| Sauvegarder | `Ctrl+s` |
| Explorer | `Espace e` |
| Récupérer tout l'espace d'écran | `Ctrl+w` puis `_` |

## Clavier AZERTY : caractères spéciaux

Avec `macos-option-as-alt = left` dans la config Ghostty, Option **gauche** sert de Alt (raccourcis) et Option **droite** tape les caractères spéciaux.

| Caractère | Touches |
|---|---|
| `{` `}` | Option droite + `(` / `)` |
| `[` `]` | Option droite + Shift + `(` / `)` |
| `\|` | Option droite + Shift + `L` |
| `\` | Option droite + Shift + `/` |
| `~` | Option droite + `N`, puis Espace |
| `/` | Shift + `:` |
| `^` | touche morte : à éviter (utiliser `0`) |

## Ghostty

| Action | Raccourci |
|---|---|
| Nouvel onglet | `Cmd+T` |
| Onglet suivant / précédent | `Ctrl+Tab` / `Ctrl+Shift+Tab` |
| Aller à l'onglet n | `Cmd+` chiffre |
| Split à droite / en bas | `Cmd+D` / `Cmd+Shift+D` |
| Naviguer entre splits | `Cmd+Option+flèches` |
| Zoomer / dézoomer un split | `Cmd+Shift+Entrée` |
| Égaliser les splits | `Cmd+Ctrl+=` |
| Redimensionner un split | `Cmd+Ctrl+flèches` |
| Fermer split / onglet | `Cmd+W` |
| Fermer tout l'onglet | `Cmd+Option+W` |
| Recharger la config | `Cmd+Shift+,` |

## Shell (zsh)

| Action | Raccourci |
|---|---|
| Mot précédent / suivant | `Option gauche+←` / `→` |
| Début / fin de ligne | `Cmd+←` / `Cmd+→` |
| Historique cherchable | `Ctrl+R` (fzf) |
| Aller dans un dossier connu | `z nom` (zoxide) |

## Nvim : ouvrir des fichiers

| Action | Raccourci |
|---|---|
| Chercher un fichier | `Espace ff` ou `Espace Espace` |
| Fichiers récents | `Espace fr` |
| Chercher dans le contenu | `Espace sg` |
| Explorer (ouvrir / fermer) | `Espace e` |
| Explorer ↔ éditeur | `Ctrl+h` / `Ctrl+l` |

## Nvim : se déplacer dans un fichier

Mode normal (après `Esc`).

| Action | Touche |
|---|---|
| Mot suivant / précédent | `w` / `b` |
| Début de ligne | `0` |
| Fin de ligne | `$` |
| Début / fin du fichier | `gg` / `G` |
| Aller à la ligne 42 | `:42` puis Entrée |
| Demi-page bas / haut | `Ctrl+d` / `Ctrl+u` |
| Paragraphe suivant / précédent | `}` / `{` (Option droite) |
| Chercher | Shift+`:` puis le texte, `n` / `N` |
| Aller à la définition | `gd` |
| Historique de positions | `Ctrl+o` / `Ctrl+i` |

## Nvim : buffers (fichiers ouverts)

| Action | Raccourci |
|---|---|
| Buffer précédent / suivant | `Shift+h` / `Shift+l` |
| Liste des buffers | `Espace ,` |
| Bascule entre les deux derniers | `Espace bb` |
| Fermer le buffer courant | `Espace bd` |
| Fermer tous les autres | `Espace bo` |
| Épingler un buffer | `Espace bp` |

## Nvim : fenêtres (splits internes)

| Action | Raccourci |
|---|---|
| Split vertical | `Ctrl+w` puis `v` |
| Split horizontal | `Espace -` ou `Ctrl+w` puis `s` |
| Naviguer entre fenêtres | `Ctrl+h/j/k/l` |
| Fermer la fenêtre courante | `Espace wd` |
| Garder uniquement la fenêtre courante | `Ctrl+w` puis `o` (ou `:only`) |
| Maximiser la hauteur de la fenêtre courante | `Ctrl+w` puis `_` |
| Égaliser la taille des fenêtres | `Ctrl+w` puis `=` |
| Zoom temporaire (bascule) | `Espace wm` |
| Terminal intégré (ouvrir / fermer) | `Ctrl+/` |

!!! note "Nvim prend la moitié de l'écran ?"
    Il reste une autre fenêtre ouverte (terminal intégré, quickfix, panneau d'aide) réduite au minimum. `Ctrl+w` puis `_` redonne tout l'espace à la fenêtre courante. Pour la retrouver : `Ctrl+w` puis `j`, puis `Espace wd` pour la fermer.

## Nvim : édition et quitter

| Action | Raccourci |
|---|---|
| Insertion / retour au mode normal | `i` / `Esc` |
| Sauvegarder | `Ctrl+s` ou `:w` |
| Annuler / refaire | `u` / `Ctrl+r` |
| Quitter tout | `Espace qq` ou `:qa` |
| Quitter sans sauvegarder | `:qa!` |

## Config Ghostty de référence

`~/.config/ghostty/config` :

```
font-family = JetBrainsMono Nerd Font
font-size = 14
theme = catppuccin-mocha
macos-option-as-alt = left
window-padding-x = 8
window-padding-y = 8
shell-integration = zsh
```
