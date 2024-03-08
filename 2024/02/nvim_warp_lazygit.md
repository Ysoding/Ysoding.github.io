> [更多配置](https://xmchxup.github.io/posts/linux/configure/)

# Warp

[Warp is a modern, Rust-based terminal with AI built in so you and your team can build great software, faster.](https://github.com/warpdotdev/Warp)

# Nvim

```sh
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux64.tar.gz
sudo rm -rf /opt/nvim
sudo tar -C /opt -xzf nvim-linux64.tar.gz
```

```sh
export PATH="$PATH:/opt/nvim-linux64/bin"
```

## NvChad

> [install](https://nvchad.com/docs/quickstart/install)

添加映射

```lua
M.xmchxup = {
  n = {
     ["<C-n>"] = {"<cmd> Telescope <CR>", "Telescope"},
  },
  i = {
     ["kj"] = { "<ESC>", "escape insert mode" , opts = { nowait = true }},
  }
}
```

帮助命令 NvCheatsheet

# Lazygit

> [simple terminal UI for git commands](https://github.com/jesseduffield/lazygit#ubuntu)

# Vim

> vscode 中有个 learn vim 插件可以学习下

# Chrome

Vimium C 插件
