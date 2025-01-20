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

### Key binding

- **Ctrl** + **K**: Comment/uncomment selected text
- **Ctrl** + **R**: Find and replace selected text
- **Alt** + **Up/Down**: Move selected text up/down a line


# NeoVim as editor

If you wish, you can use **NeoVim** as your editor for **Godot** alongside a running instance of **Godot**. For that, you will have to install **NeoVim**, and then install either **lazy.vim** or **kickstart.nvim**. Also, you will have to change some configurations in **Godot**.

## 1. Godot Configuration

- Make the following changes in Godot `Editor`->`Editor Settings...`:
  - **Network** -> **Language Server**:
    - Remote Host: `127.0.0.1`
    - Remote Port: `6005`
    - Enable Smart Resolve: `On`
    - Show Native Symbols in Editor: `On`

## 2. Install last version of NeoVim (Ubuntu)

```bash
$ sudo add-apt-repository ppa:neovim-ppa/unstable -y
$ sudo apt update
$ sudo apt install make gcc ripgrep unzip git xclip neovim
```

or

```
$ sudo apt-get install neovim
```

### Launch NeoVim

```bash
$ nvim
```

## 3. vim-plug

### Installation and configuration

- Download vim vim-plug

`sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'`

- Add vim-plug and plugin names to `~/.config/nvim/init.vim` file:

```
" vim-plug
call plug#begin('~/.vim/plugged')

Plug 'nvim-treesitter/nvim-treesitter' " Treesitter for better syntax highlighting
Plug 'neovim/nvim-lspconfig'    " LSP Configuration
Plug 'mfussenegger/nvim-dap'	" Debugger
Plug 'habamax/vim-godot'

" end vim-plug
call plug#end()
```

- If you are editing the `~/.config/nvim/init.vim` file with NeoVim, save and exit.

- Launch NeoVim again and run: `:PlugInstall`

- Make the following changes in Godot `Editor`->`Editor Settings...`:
  - **Text Editor** -> **External**:
    - Use External Editor: `On`
    - Exec Path: `/usr/bin/nvim`
    - Exec Flags: `--server /tmp/godot.pipe --remote-send "<esc>:n {file}<CR>:call cursor({line},{col})<CR>"`

## 4. lazy.vim

### 4.1. Installation and configuration

- Download lazy.vim starter:

`$ git clone https://github.com/LazyVim/starter.git`

- From the downloaded `starter` directory, copy to `~/.config/nvim/` the following: `init.lua`, `stylua.toml` and `lua/`

- Launch NeoVim. Lazy.vim will start installing and configuring plugins automatically.

- When all plugins are installed, add this snipped in `~/.config/nvim/lua/plugins/example.lua`

```lua
  -- add GDSCript to lspconfig
  {
    require("lspconfig")["gdscript"].setup({
      name = "godot",
      cmd = vim.lsp.rpc.connect("127.0.0.1", 6005),
    }),
  },
```
- Make the following changes in Godot `Editor`->`Editor Settings...`:
  - **Text Editor** -> **External**:
    - Use External Editor: `On`
    - Exec Path: `/usr/bin/nvim`
    - Exec Flags: `--server /tmp/godot.pipe --remote-send "<esc>:n {file}<CR>:call cursor({line},{col})<CR>"`

### 4.2. Usage

1. Start NeoVim like this: `$ nvim --listen /tmp/godot.pipe`.
2. Launch Godot and open a project.
3. An automatic connection should happen with the following message in Godot: `[LSP] Connection Taken`
4. Go back to NeoVim to start editing. Any saved change will reflected in the Godot editor.
5. If an automatic connection is not happening, click on a `.gd` file within Godot editor.

## 5. kickstart.nvim

### 5.1. Installation and configuration

Readme Page: [https://github.com/nvim-lua/kickstart.nvim?tab=readme-ov-file](https://github.com/nvim-lua/kickstart.nvim?tab=readme-ov-file)
- Download lazy.vim starter: `$ git clone https://github.com/nvim-lua/kickstart.nvim.git "${XDG_CONFIG_HOME:-$HOME/.config}"/nvim`

- Launch NeoVim. Kickstart.vim will start installing and configuring plugins automatically.

- When all plugins are installed, edit `~/.config/nvim/init.lua` :
  - Under `capabilities = vim.tbl_deep_extend('force', capabilities, require('cmp_nvim_lsp').default_capabilities())` add these lines:
    
    ```lua
          require('lspconfig').gdscript.setup { capabilities = capabilities }
      vim.keymap.set('n', '<leader>sg', function() vim.fn.serverstart '127.0.0.1:6005' end, { noremap = true })

    ```

  - Add `gdscript` to this line:
    ```lua
    ensure_installed = { 'gdscript', 'go', 'bash', 'c', 'diff', 'html', 'lua', 'luadoc', 'markdown', 'markdown_inline', 'query', 'vim', 'vimdoc' },
    ```

### 5.2. Usage

1. Launch **Godot**.
2. Launch **NeoVim**.

If you get the error: `could not connect to 127.0.0.1:6005, reason : "ECONNREFUSED"`, go to your **Godot** editor and be sure that the server settings at **Editor** -> **Editor Settings** -> **Network** -> **Language Server** are correctly setup.

### 5.3. Useful commands
- **Space** + **s** + **g** -\> Search for any word in the whole project
- **Space** + **s** + **w** -\> Search for the word where the cursor is on in the whole project
- **Space** + **s** + **f** -\> Search for files in the project
- **g** + **d** -\> Go to Definition of the function/variable where the cursor is on
- **g** + **D** -\> Go to Declaration of the function/variable where the cursor is on
- **g** + **I** -\> Go to Implementation of the function/variable where the cursor is on
- **Ctl** + **o** -\> Go back to previous window after going to Definition/Declaration/Implementation
- **:e .** -\> Open file explorer


# Tmux

### Installation

```
$ sudo apt install tmux
```
### Color and mouse scroll configuration

```
$ touch tmux.conf
$ echo "set -g default-terminal \"screen-256color\"" >> tmux.conf
$ echo "set -g mouse on" >> tmux.conf
$ sudo chown root:root tmux.conf
$ sudo mv tmux.conf /etc
```

### Key bindings

- Leader: **Ctrl** + **b**
- New window: **leader** + **c**
- Cycle throug windows: **leader** + **n**
- Select window: **leader** + **number**
- New vertical pane: **leader** + **%**
- New horizontal pane: **leader** + **"**
- Move between panes: **leader** + **arrows**
- Resize pane: **hold leader** + **arrows**
- Detach from Tmux: **leader** + **d**

Bash:
- `$ tmux ls`: List sessions
- `$ tmux attach`: Reattach to tmux

Create new session:
1. Detach from tmux
2. Run tmux

- List sessions: **leader** + **s**
- Change session: ***leader** + **s*** and choose
- List all windows in all sessions: **leader** + **w**
- Change session and/or window: **leader** + **w** and choose

COMMAND MODE
- Enter command mode: **leader** + **:**

Then:

- Rename window: `rename-window` + *your_window_name*
- Rename session: `rename-session` or `rename` + *your_session_name*


# Aseprite 

You can build **Aseprite** from source code. When building from source code, you can use it for [free](https://www.aseprite.org/faq/#if-aseprite-source-code-is-available-how-is-that-you-are-selling-it) for your personal purposes. You can make commercial art/assets with it too. The only restriction is that you cannot redistribute Aseprite to third parties.

Ref:
- [https://www.aseprite.org/faq/#if-aseprite-source-code-is-available-how-is-that-you-are-selling-it](https://www.aseprite.org/faq/#if-aseprite-source-code-is-available-how-is-that-you-are-selling-it)
- [https://github.com/aseprite/aseprite/blob/main/INSTALL.md](https://github.com/aseprite/aseprite/blob/main/INSTALL.md)
- [https://github.com/aseprite/skia?tab=readme-ov-file#skia-on-linux](https://github.com/aseprite/skia?tab=readme-ov-file#skia-on-linux)

### 1. Download Aseprite source code

```
git clone --recursive https://github.com/aseprite/aseprite.git
```
### 2. Install dependencies
```
sudo apt-get install -y g++ clang libc++-dev libc++abi-dev cmake ninja-build libx11-dev libxcursor-dev libxi-dev libgl1-mesa-dev libfontconfig1-dev python-is-python3
```

### 3. Skia for Aseprite and laf 

```
mkdir $HOME/deps
cd $HOME/deps
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
git clone -b aseprite-m102 https://github.com/aseprite/skia.git
export PATH="${PWD}/depot_tools:${PATH}"
cd skia
python3 tools/git-sync-deps
```

```
 gn gen out/Release-x64 --args='is_debug=false is_official_build=true skia_use_system_expat=false skia_use_system_icu=false skia_use_system_libjpeg_turbo=false skia_use_system_libpng=false skia_use_system_libwebp=false skia_use_system_zlib=false skia_use_sfntly=false skia_use_freetype=true skia_use_harfbuzz=true skia_pdf_subset_harfbuzz=true skia_use_system_freetype2=false skia_use_system_harfbuzz=false cc="clang" cxx="clang++" extra_cflags_cc=["-stdlib=libc++"] extra_ldflags=["-stdlib=libc++"]'
```

```
ninja -C out/Release-x64 skia modules
```


### 4. Compile aseprite
```
cd aseprite
mkdir build
cd build
export CC=clang
export CXX=clang++
cmake \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DCMAKE_CXX_FLAGS:STRING=-stdlib=libc++ \
  -DCMAKE_EXE_LINKER_FLAGS:STRING=-stdlib=libc++ \
  -DLAF_BACKEND=skia \
  -DSKIA_DIR=$HOME/deps/skia \
  -DSKIA_LIBRARY_DIR=$HOME/deps/skia/out/Release-x64 \
  -DSKIA_LIBRARY=$HOME/deps/skia/out/Release-x64/libskia.a \
  -G Ninja \
  ..
ninja aseprite
```

### 5. Launch aseprite

The executable file is created in `aseprite/build/bin/`
