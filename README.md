# Zsh Configuration (.zshrc)

WARNING: Following sentences are written by Chatgpt automatically.

This repository contains a customized `.zshrc` configuration designed to improve usability and efficiency when working with Zsh.
It focuses on better Git visibility, enhanced command completion, and reduced typing overhead, while intentionally avoiding external frameworks such as Oh My Zsh.

---

## Concept

This configuration emphasizes lightness, explicit behavior, and fast startup time.
By relying only on standard Zsh features and avoiding opaque plugin systems, each setting is easy to understand and maintain.
The goal is to make shell behavior predictable and transparent.

---

## Prompt Configuration

### Left Prompt (PROMPT)

The left prompt displays the user name, host name, number of background jobs, and the current working directory.
When the directory path becomes deeply nested, it is automatically shortened to maintain readability.

    PROMPT='
    %F{cyan}[%n@%m](%*%)%F{white}: %(5~,%-1~/.../%2~,%~)%f
    %F{white}%B> %f'

---

### Right Prompt (RPROMPT)

The right prompt displays the current Git repository status.
When the working directory is inside a Git repository, the active branch name and working tree state are shown automatically.

    autoload -Uz vcs_info
    setopt prompt_subst

    precmd () {
      vcs_info
    }

    RPROMPT=$RPROMPT'${vcs_info_msg_0_}'

---

## Git Status Display

Git status information is managed using `vcs_info`.
The display format is customized so that staged and unstaged changes can be visually distinguished at a glance.

    zstyle ':vcs_info:*' check-for-changes false
    zstyle ':vcs_info:*' unstagedstr "%F{red}%B＋%b%f"
    zstyle ':vcs_info:*' stagedstr "%F{yellow}★ %f"
    zstyle ':vcs_info:*' formats "%u%c%F{cyan}【 %b 】%f"
    zstyle ':vcs_info:*' actionformats '【%b | %a】'

This allows immediate recognition of repository state without explicitly running Git commands.

---

## Git Aliases

To reduce typing effort during daily Git operations, commonly used commands are aliased.
Git command completion is also enabled for the short alias.

    alias g="git"
    compdef g=git

    alias gs='git status --short --branch'
    alias ga='git add -A'
    alias gc='git commit -m'
    alias gps='git push'
    alias gpsu='git push -u origin'
    alias gp='git pull origin'
    alias gf='git fetch'
    alias gfp='git fetch -p'
    alias gl='git log --abbrev-commit --no-merges --date=short --date=iso'
    alias glg='git log --abbrev-commit --no-merges --date=short --date=iso --grep'
    alias glc='git log --abbrev-commit --no-merges --date=short --date=iso origin/html..html'
    alias gd='git diff'
    alias gco='git checkout'
    alias gcob='git checkout -b'
    alias gb='git branch'
    alias gba='git branch -a'
    alias gbr='git branch -r'
    alias gm='git merge'
    alias gr='git reset'

---

## Command Completion

Zsh’s built-in completion system is enabled to improve command discovery and input efficiency.

    autoload -U compinit
    compinit -u

Completion candidates are colorized to improve readability.

    autoload -U colors
    colors
    zstyle ':completion:*' list-colors "${LS_COLORS}"

Completion behavior is further refined as follows.

    setopt complete_in_word
    zstyle ':completion:*:default' menu select=1
    zstyle ':completion::complete:*' use-cache true
    zstyle ':completion:*' matcher-list 'm:{a-z}={A-Z}'
    setopt list_packed

Custom completion definitions can be placed in the following directory.

    fpath=(~/.zsh/completion $fpath)

---

## Command Correction

Automatic command correction is enabled to catch typing mistakes before execution.

    setopt correct
    SPROMPT="correct: %R -> %r ? [Yes/No/Abort/Edit] => "

This helps prevent accidental execution of mistyped commands.

---

## Directory Navigation Enhancements

Directories can be entered directly without explicitly typing the `cd` command.

    setopt auto_cd

A directory stack is also maintained, allowing easy navigation between previously visited directories.

    setopt auto_pushd

---

## Tree-like Directory View

A custom alias is defined to display directory structures in a tree-like format without relying on external tools.

    alias tree="pwd;find . | sort | sed '1d;s/^\.//;s/\/\([^/]*\)$/|--\1/;s/\/[^/|]*/| /g'"

---

## ZLE Key Bindings

### Git Commit Template

A custom ZLE widget inserts a three-line Git commit command to encourage structured commit messages.

    function git_commit() {
        BUFFER='git commit -m "#'
        CURSOR=$#BUFFER
        BUFFER=$BUFFER'" -m "" -m ""'
    }
    zle -N git_commit
    bindkey '^[git_commit' git_commit

---

### Terminal Tab Renaming

This widget allows terminal tabs to be renamed by emitting an escape sequence.
It is intended to be used in combination with custom key bindings in terminal emulators such as iTerm2.

    function tab_rename() {
        BUFFER="echo -ne \"\e]1;"
        CURSOR=$#BUFFER
        BUFFER=$BUFFER\\a\"
    }
    zle -N tab_rename
    bindkey '^[tab_rename' tab_rename

---

### Word-Level Deletion

Key bindings are configured to delete words forward and backward efficiently.

    bindkey '^[word-remove-right' kill-word
    bindkey '^[word-remove-left' backward-kill-word

---

## History Management

Command history is shared across multiple terminal sessions and duplicate entries are automatically removed.

    HISTFILE=~/.zsh_history
    HISTSIZE=10000
    SAVEHIST=10000
    setopt append_history
    setopt share_history
    setopt hist_ignore_all_dups

---

## Conda Initialization (make this disable)

Official initialization logic for Anaconda or Miniconda is included.

    export PATH="$PATH:/home/xyz/anaconda3/condabin"

---

## AWS CLI Completion (make this disable)

Command completion for the AWS CLI is enabled.

    source /usr/local/bin/aws_zsh_completer.sh

---

## Additional Settings

The DISPLAY environment variable is set to enable GUI applications.

    export DISPLAY=:0.0

Support for command-not-found suggestions is also enabled.

    source /etc/zsh_command_not_found

---

## Summary

This `.zshrc` configuration aims to provide high usability and visibility using only standard Zsh features.
It is well suited for Git-centric development environments and for users who prefer explicit and maintainable shell configurations.
