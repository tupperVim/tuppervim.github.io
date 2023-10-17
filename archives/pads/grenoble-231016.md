# TupperVim - Grenobe 16 oct 2023

## Gestion des _sessions_

Support natif des sessions dans `vim` et `nvim` avec `:mksession`.
Cette commande prend un argument optionnel qui est le chemin vers le fichier de session.
Par défaut c'est `$(pwd)/Session.vim`.

Pour le charger, deux approches:
1. `(n)vim -S` qui va chercher la session par défaut ou prend le chemin vers le fichier généré auparavant.
2. `(n)vim` puis `:source $PATH` où `$PATH` est évidemment le chemin vers le fichier généré auparavant.

### Plugins

#### Maintient à jour des `Session.vim`

Le problème de l'approche 100% native est qu'il faut penser à rappeler `:mksession` à chaque changement (ouverture et fermeture de fenêtre, changement de buffer dans une fenêtre etc).
Il existe [le plugin Obsession de Tim POPE](https://github.com/tpope/vim-obsession) qui s'occupe de faire cela pour nous une bonne fois pour toute.
Fonctionne pour `vim` et `nvim`.

#### Correction des layouts

Parfois, sur une fausse manipulation, on foire le layout qui va bien.
Une fenêtre est fermée, on arrive plus à la remettre au bon endroit facilement.

Là, c'est [le plugin WinShift](https://github.com/sindrets/winshift.nvim) qui nous vient en aide.
Il permet d'interactivement déplacer les fenêtres de manière très intuitive et précise.
Ne fonctionne que pour `nvim` malheureusement.

### Bonus

Auto-promo, [une présentation sur mon _workflow_](https://pcoves.gitlab.io/my-tmux-vim-workflow).
TL;DR: `Alacritty` -> `tmux` -> `nvim` le tout avec des sessions automagiques.

## Gestion des conflits `git` avec `(n)vim`

Un peu de [configuration pour `git`](https://gitlab.com/pcoves/git/-/blob/main/.config/git/config?ref_type=heads#L13-15).

```config
[merge]
    tool = nvimdiff
    conflictstyle = diff3
```

Ce blog demande à `git mergetool` d'ouvrir `nvim` avec trois fenêtres en haut| Local | Plus proche parent commun | Distant | et une fenêtre en bas | Résultat du _merge_ |.
J'utilise ensuite `]c` et `[c` pour aller aux conflits suivants et précédents respectivement.
Puis `:diffget LOCAL/REMOTE/PARENT<CR>` pour prendre les parties qui m'intéressent ou simplement éditer la fenêtre de résultat manuellement.

### Plugin

Étant donné que c'est la commande `git mergetool` qui lance cela, je préfère quitter `nvim` pour faire mes `git merge` ou `git rebase` avec d'autres branches.
Le reste (`commit` et autres opérations courantes), je le confie à [fugitive, un autre plugin de Tim POPE](https://github.com/tpope/vim-fugitive).

#### Bonus

Ma [configuration pour `fugitive`](https://gitlab.com/pcoves/nvim/-/blob/2819859fed41436a2ee1fb0e82f7e7628bfe00d4/.config/nvim/lua/plugins/init.lua#L69-88) et surtout une paire de dépendances optionnelles:

```lua
{
    "tpope/vim-fugitive",
    name = "fugitive",
    dependencies = {
        { "tpope/vim-rhubarb",             name = "fugitive-github" },
        { "shumphrey/fugitive-gitlab.vim", name = "fugitive-gitlab" },
    },
    config = function() require("plugins.fugitive") end,
    cmd = { "Git", "G", "Gw", "Gread", "GBrowse" }
},
{
    "ruifm/gitlinker.nvim",
    name = "gitlinker",
    config = function() require("gitlinker").setup() end,
    keys = {
        { "<Leader>gy", mode = "n" },
        { "<Leader>gy", mode = "v" }
    }
},
```

Pour avoir `:GBrowse` ouvrir mon navigateur par défaut sur le code et `<Leader>gy` copier l'URI vers `github` ou `gitlab` pour partager facilement des exemples avec les collègues.
