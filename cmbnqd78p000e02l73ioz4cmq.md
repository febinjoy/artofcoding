---
title: "Part 2: Essential Terminal Tools for Productivity"
seoTitle: "Boosting Developer Productivity with Zsh, Neovim, and TMUX on Ubuntu"
seoDescription: "Supercharge your terminal with Zsh, Neovim, TMUX, and essential CLI tools. Step-by-step guide tailored for Ubuntu homelab setups."
datePublished: Sun Jun 08 2025 14:00:23 GMT+0000 (Coordinated Universal Time)
cuid: cmbnqd78p000e02l73ioz4cmq
slug: part-2-essential-terminal-tools-for-productivity
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748866851140/ada8d28d-278b-4cea-856d-9cc8c12aedec.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1748866997020/bb5fd5f6-6b8d-48da-b839-f785dbafb57e.png
tags: terminal, neovim, zsh, tmux, developer-setup

---

Welcome to Part 2 of the Ubuntu home lab setup series. In this section, we’ll focus on making the terminal experience powerful and developer-friendly. We’ll install popular utilities, configure a rich shell environment with Zsh, and set up Neovim and TMUX.

---

## 🛠️ Step 1: Install Utilities

Install a collection of essential terminal tools:

```bash
sudo apt install -y \
  net-tools \
  zsh \
  bat \
  unzip \
  ripgrep \
  grep \
  xclip \
  htop \
  ranger \
  iptables \
  fzf
```

* `net-tools`: A collection of networking tools, which includes some of the popular tools like `ifconfig` and `netstat`. They are very handy in managing network interfaces and connections.
    
* `zsh`: A powerful shell which complements bash functionality with a strong scripting capability and personalisation options.
    
* `bat`: Better `cat` with syntax highlighting.
    
* `unzip`: Utility for unpacking ZIP files.
    
* `ripgrep` and `fzf`: Search and fuzzy finding.
    
* `grep`: Another search tool for searching plain-text data on the command line for lines that match a regular expression
    
* `xclip`: A command line interface to the X11 clipboard, which can be used to copy and paste text or files. If you install NeoVim, this is a must-have to copy text to and from NeoVim out of the terminal.
    
* `htop`: Interactive process viewer.
    
* `ranger`: Console file manager.
    
* `iptables`: It is a Linux command line utility to manage the Linux kernel firewall. It is implemented within the `netfilter` framework. We can define rules to either accept a packet or reject it.
    

---

## 🌀 Step 2: Install LazyGit

LazyGit is a simple terminal UI for Git:

```bash
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": *"v\K[^"]*')
curl -Lo lazygit.tar.gz \
  "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit
sudo install lazygit -D -t /usr/local/bin/
```

---

## 🎨 Step 3: Install Git-Delta

Git-delta is a syntax-highlighting pager for `git`, `diff`, `grep`, and `blame` output. It's designed to make developer’s life easier by making reviewing code changes easier and efficient. It improves the readability and layout of diffs:

1. Download delta from the [official page](https://dandavison.github.io/delta/installation.html).
    
2. Optional: Install a theme configuration file like `themes.gitconfig` into `~/.config/delta`.
    
3. Add to `~/.gitconfig`:
    

```ini
[core]
  pager = delta
[interactive]
  diffFilter = delta --color-only
[include]
  path = ~/.config/delta/themes.gitconfig
[delta]
  features = arctic-fox
  navigate = true
  line-numbers = true
  side-by-side = true
[merge]
  conflictstyle = zdiff3
```

---

## 📝 Step 4: Install Neovim

NeoVim is my favourite text editor of choice. Even though I use `nano` for most steps in this document for convenience. Follow the steps below to install NeoVim.

To get the latest version of Neovim:

```bash
sudo add-apt-repository ppa:neovim-ppa/stable
sudo apt update
sudo apt install -y neovim
```

If you want to configure NeoVim to have support for LSP servers, Fuzzy Finding, Lazy and Mason Plugin support, Linting and automatic code suggestions, follow the instructions here : [GitHub - febinjoy/febins-neovim-config](https://github.com/febinjoy/febins-neovim-config). [It contains my Neo-Vim configuration.](https://github.com/febinjoy/febins-neovim-config) It requires you to create a `.config/nvim` folder in your home directory. All the configurations I made are using Lua. It is simple, all you will need to do is to clone the repo and let NeoVim install the Lazy and Mason plugins.

Run this to use my config:

```bash
git clone https://github.com/febinjoy/febins-neovim-config ~/.config/nvim
```

Launch `nvim` to auto-install plugins.

---

## 🐚 Step 5: Configure Zsh

1. **Install Zsh (already done in utilities)**
    
2. **Create history and config files**:
    

```bash
touch ~/.zsh_history
nano ~/.zshrc
```

3. **Add the following to** `~/.zshrc`:
    

```bash
ZINIT_HOME="${XDG_DATA_HOME:-${HOME}/.local/share}/zinit/zinit.git"
if [ ! -d $ZINIT_HOME ]; then
  mkdir -p "$(dirname $ZINIT_HOME)"
  git clone https://github.com/zdharma-continuum/zinit.git "$ZINIT_HOME"
fi
source "$ZINIT_HOME/zinit.zsh"

zinit ice depth=1; zinit light romkatv/powerlevel10k
zinit light zsh-users/zsh-autosuggestions
zinit light zsh-users/zsh-syntax-highlighting
zinit light Aloxaf/fzf-tab
zinit snippet OMZP::git
zinit snippet OMZP::sudo
zinit snippet OMZP::extract

export HISTFILE=~/.zsh_history
export HISTSIZE=10000
export SAVEHIST=10000
bindkey "^[[3~" delete-char

# Aliases
alias ls='ls --color'
alias grep='grep --color'
```

4. **Change Default Shell to Zsh**:  
    Installing Zsh and having the config will not automatically switch your shell from bash to Zsh. We need to use the following command to change your shell:
    

```bash
chsh -s $(which zsh)
```

Then reboot or re-login.

> ✨ If using Powerlevel10k, you may want a Nerd Font on your terminal.

5. **My final version**:  
    I have added more options and aliases to my `.zshrc` according to my preference. Please find it below. Please don't hesitate to use it. You may need to remove some of it if you don’t have the associated tool installed. e.g. fzf:
    

```bash
# Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
# Initialization code that may require console input (password prompts, [y/n]
# confirmations, etc.) must go above this block; everything else may go below.
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

ZINIT_HOME="${XDG_DATA_HOME:-${HOME}/.local/share}/zinit/zinit.git"
if [ ! -d $ZINIT_HOME ]; then
  mkdir -p "$(dirname $ZINIT_HOME)"
  git clone https://github.com/zdharma-continuum/zinit.git "$ZINIT_HOME"
fi
source "${ZINIT_HOME}/zinit.zsh"

zinit ice depth=1; zinit light romkatv/powerlevel10k

# Add in zsh plugins
zinit light zsh-users/zsh-autosuggestions
zinit light zsh-users/zsh-syntax-highlighting
zinit light Aloxaf/fzf-tab

# Add in snippets
zinit snippet OMZP::git
zinit snippet OMZP::sudo
zinit snippet OMZP::extract


# Load zsh-completions
autoload -U compinit && compinit

# Key bindings
bindkey '^]' autosuggest-accept
bindkey '^p' history-search-backward
bindkey '^n' history-search-forward
bindkey "^[[3~" delete-char

# Setup zsh-history-file
export HISTFILE=~/.zsh_history
export HISTSIZE=10000
export SAVEHIST=$HISTSIZE
export HISTDUP=erase

# Options
setopt APPEND_HISTORY
setopt SHARE_HISTORY
setopt HIST_IGNORE_SPACE
setopt HIST_SAVE_NO_DUPS
setopt HIST_IGNORE_DUPS
setopt HIST_FIND_NO_DUPS
setopt EXTENDED_HISTORY
setopt HIST_EXPIRE_DUPS_FIRST
setopt HIST_IGNORE_ALL_DUPS
setopt HIST_VERIFY

# Aliases
alias ls='ls --color'
alias grep='grep --color'
alias fgrep='fgrep --color'
alias egrep='egrep --color'

# Completion styling
zstyle ':completion:*' matcher-list 'm:{a-z}={A-Za-z}'
zstyle ':completion:*' list-colors "${(s.:.)LS_COLORS}"
zstyle ':completion:*' menu no
zstyle ':fzf-tab:complete:cd:*' fzf-preview 'ls --color=always $realpath'
zstyle ':fzf-tab:complete:__zooxide_z:*' fzf-preview 'ls --color=always $realpath'

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh
```

---

## 📦 Step 6: Install and Configure TMUX

TMUX aka Terminal Multiplexer is a powerful utility for terminal management, enabling the user to work with several terminal sessions in one window. It is a game changer in my opinion, which allows the user to easily create, manage, and switch between multiple terminal panes and windows, enhancing productivity and making multitasking easier. TMUX allows session detachment and re-attachment, which is very helpful in keeping persistent sessions on remote servers. It is also highly customisable and is an indispensable utility for any developer, system administrator, or anyone else who works with the command line on a frequent basis.

1. Install TMUX:
    

```bash
sudo apt install -y tmux
```

2. Create config file:
    

```bash
nano ~/.tmux.conf
```

Paste [my config](https://github.com/febinjoy/terminal-config/blob/main/.tmux.conf) or your own.

3. Install TPM (Plugin Manager):
    

```bash
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

4. Inside a TMUX session:
    

* Press `Ctrl + A` then `I` to install plugins
    
* Press `Ctrl + A` then `r` to reload
    
* If you used my TMUX configuration, you will see a themed TMUX window at this point.
    

---

In Part 3, we'll focus on securing and hardening the server with firewall rules, SSH configuration, Fail2Ban, and more.

Stay tuned for **Part 3: Securing and Hardening the Server**!