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
5. Type `godot` in the **'Name'** field
6. Type `/usr/local/bin/your_godot_executable_file_name` in the **'Command Name'**
7. Tick the box **'Use dedicated GPU if available'**
8. Clic on the **'OK'** button

### Key binding

- **Ctrl** + **K**: Comment/uncomment selected text
- **Ctrl** + **R**: Find and replace selected text
- **Alt** + **Up/Down**: Move selected text up/down a line


# NeoVim as editor

If you wish, you can use **NeoVim** as your editor for **Godot** alongside a running instance of **Godot**. For that, you will have to install **NeoVim**, and then install **vim-plug**. Also, you will have to change some configurations in **Godot**.

## 1. Godot Configuration

- Make the following changes in Godot `Editor`->`Editor Settings...`:
    - **Network** -> **Language Server**:
        - Remote Host: `127.0.0.1`
        - Remote Port: `6005`
        - Enable Smart Resolve: `On`
        - Show Native Symbols in Editor: `On`
    - **Text Editor** -> **External**:
        - Use External Editor: `On`
        - Exec Path: `/usr/bin/nvim`
        - Exec Flags: `--server /tmp/godot.pipe --remote-send "<C-\><C-N>:n {file}<CR>:call cursor({line},{col})<CR>"`

## 2. NeoVim Installation - last version (Ubuntu)

```bash
$ sudo add-apt-repository ppa:neovim-ppa/unstable -y
$ sudo apt update
$ sudo apt install make gcc ripgrep unzip git xclip neovim
```

or

```
$ sudo apt-get install neovim
```

## 3. NeoVim Configuration

### Config file and vim-plug installation

- Download this custom [init.lua](https://drive.google.com/file/d/14HtGZQ8FPXYxhtkC2kvlnWj35U-n7n1D/view?usp=drive_link) configuration file into `~/.config/nvim/` directory. This file contains configuration settings for NeoVim, including the list of plugins to be installed via vim-plug.

- Install vim-plug:

`sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'`

- If you don't have `git` and `libc6-dev` installed, run the command: `sudo apt install git libc6-dev`. They are needed for the next steps.

- Launch NeoVim by running the command `nvim` in your terminal. You'll see a bunch of errors. They will be fixed when we install the plugins. Just press enter.

- To install the plugins, within NeoVim type: `:PlugInstall`

- Vim-plug will start installing a bunch of plugins and languages specified in the init.lua config file. When it finished, restart NeoVim.

- Check that **gdscript** is added to the Treesitter parser by typing: `:checkhealth nvim-treesitter`

- If you don't see **gdscript** added, type: `:TSUpdate` and restart NeoVim.


## 4. Usage

- Open both Godot and NeoVim. It doesn't matter which one you open first.
- In Godot, double click on a gdscript file.
- If successful, a log message in Godot will appear: `[LSP] Connection Taken`. You can now start editing the file in NeoVim.
- Changes will apply each time you save in NeoVim.
- If you double click on a new gscript file in Godot, the new file will open in the same instance and the same panel of NeoVim, hiding the previous code.
- Godot will ignore any new tab you open in NeoVim, so if you open a new tab and double click in another gdscript file in Godot, it will open in the original tab, and not the new tab.
- If you want to have several gdscript files open in the same NeoVim instance:
    1. Split NeoVim screen with the commands `:sp` or `:vs`
    2. Move to the new panel with `Ctrl` + `w` + `w`
    3. In Godot, double click on the new gdscript
    4. You can repeat this process splitting further the NeoVim screen.
    5. When switching panels on NeoVim, be sure to click on the corresponding gdscript in Godot. Otherwise, the changes will not sync.
- When you close the instance/tab in NeoVim, a log message will appear in Godot: `[LSP] Disconnected`

## 5. Key bindings

- **Ctrl** + **]**: Jump to the definition of a function.
- **Ctrl** + **k**: Read documentation of a fuction.

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
