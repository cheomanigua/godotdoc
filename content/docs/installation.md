---
weight: 100
title: "Installation & Setup"
description: ""
icon: "article"
date: "2024-08-30T09:11:17+02:00"
lastmod: "2024-08-30T09:11:17+02:00"
draft: false
toc: true
---

# Installation

1. Go to [godotengine.org](https://godotengine.org/) and click on the **Download Latest** button. After that you can choose between the **Godot Engine** version or the **Godot Engine - .NET** version. If you want to use **GDScript** for your code, choose the **Godot Engine** version. If you want to use **C#** for your code, choose **Godot Engine - .NET** version. The website will automatically detect your operating system and download the proper file.

{{< alert text="All the examples on this documentation site use **GDScript**" />}}

2. Unzip the downloaded file and move the executable file to a directory of your choice. Once in that directory, run Godot by typing:

```
./Godot_v4.3-stable_linux.x86_64
```
You can rename the file if you want. `./Godot_v4.3-stable_linux.x86_64` is a single executable file, and doesn't install anything.

### Adding Godot to the launch menu

If you prefer to launch **Godot** from your desktop menu, you can add it to the launch menu.

{{< alert text="Instructions for **Cinnamon** desktop environment" />}}

1. Move your **Godot** executable file to `/usr/local/bin` directory
2. Right click on the **'Menu'** icon and select **'Edit menu'**
3. Select the menu you want to add **Godot** on the left.
4. Click on **'New Item'**
5. Type **godot** in the **'Name'** field
6. Type **/usr/local/bin/your_godot_executable_file_name** in the **'Command Name'**
7. Tick the box **'Use dedicated GPU if available'**
8. Clic on the **'OK'** button

# NeoVim as editor

If you wish, you can use **NeoVim** also as your editor for **Godot**.

## Install last version of NeoVim (Ubuntu)

```bash
$ sudo add-apt-repository ppa:neovim-ppa/unstable -y
$ sudo apt update
$ sudo apt install make gcc ripgrep unzip git xclip neovim
```

### Launch NeoVim

```bash
$ nvim
```

## kickstart.nvim

Install **kickstart** config file for an easy configuration.

Readme Page: [https://github.com/nvim-lua/kickstart.nvim?tab=readme-ov-file](https://github.com/nvim-lua/kickstart.nvim?tab=readme-ov-file)

### Installation
```bash
$ git clone https://github.com/nvim-lua/kickstart.nvim.git "${XDG_CONFIG_HOME:-$HOME/.config}"/nvim
$ nvim
```

### Edit config file
Within NeoVim, type: `:e $MYVIMRC`

### GDScript support
1. Edit the config file and under `capabilities = vim.tbl_deep_extend('force', capabilities, require('cmp_nvim_lsp').default_capabilities())` add these lines:
```lua
      require('lspconfig').gdscript.setup { capabilities = capabilities }
      vim.keymap.set('n', '<leader>sg', function() vim.fn.serverstart '127.0.0.1:6005' end, { noremap = true })
```

2. Edit the config file and add 'gdscript' to this line:
```lua
ensure_installed = { 'gdscript', 'go', 'bash', 'c', 'diff', 'html', 'lua', 'luadoc', 'markdown', 'markdown_inline', 'query', 'vim', 'vimdoc' },
```
Run `:TSUpdate`

4. Restart your computer

If you get the error: `could not connect to 127.0.0.1:6005, reason : "ECONNREFUSED"`, go to your **Godot** editor and be sure that the server settings at **Editor** -> **Editor Settings** -> **Network** -> **Language Server** are correctly setup.

### Useful commands
- **Space** + **s** + **g** -\> Search for any word in the whole project
- **Space** + **s** + **w** -\> Search for the word where the cursor is on in the whole project
- **Space** + **s** + **f** -\> Search for files in the project
- **g** + **d** -\> Go to Definition of the function/variable where the cursor is on
- **g** + **D** -\> Go to Declaration of the function/variable where the cursor is on
- **g** + **I** -\> Go to Implementation of the function/variable where the cursor is on
- **Ctl** + **o** -\> Go back to previous window after going to Definition/Declaration/Implementation
- **:e .** -\> Open file explorer
