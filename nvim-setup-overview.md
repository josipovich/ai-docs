# Neovim Setup - Features Overview

Neovim 0.11.6 with kickstart.nvim + custom Rust/Bevy plugins.
Leader key: `<Space>`

---

## Core Plugins (kickstart.nvim defaults)

### telescope.nvim - Fuzzy Finder
Find anything instantly with fuzzy search.

| Keymap | Action |
|--------|--------|
| `<leader>sf` | Search files |
| `<leader>sg` | Search by grep (live) |
| `<leader>sw` | Search current word |
| `<leader>sd` | Search diagnostics |
| `<leader>sr` | Resume last search |
| `<leader>sh` | Search help tags |
| `<leader>sk` | Search keymaps |
| `<leader>ss` | Search select (telescope pickers) |
| `<leader>s.` | Search recent files |
| `<leader>sn` | Search Neovim config files |
| `<leader><leader>` | Find existing buffers |
| `<leader>s/` | Search in open buffers |
| `<leader>/` | Fuzzy search current buffer |

### which-key.nvim - Keybinding Helper
Press any leader key and wait - a popup shows all available continuations. Groups:
- `<leader>s` - Search
- `<leader>t` - Toggle
- `<leader>h` - Git Hunk
- `<leader>r` - Rust
- `<leader>d` - Debug
- `<leader>c` - Crates (in Cargo.toml)

### nvim-lspconfig + mason.nvim - Language Server Protocol
IDE features powered by language servers. Mason auto-installs servers.

| Keymap | Action |
|--------|--------|
| `grd` | Go to definition |
| `grr` | Go to references |
| `gri` | Go to implementation |
| `gO` | Document symbols |
| `grn` | Rename symbol |
| `gra` | Code action |
| `<leader>th` | Toggle inlay hints |

Run `:Mason` to manage installed language servers and tools.

### blink.cmp - Completion Engine
Auto-completion with snippet support (LuaSnip).

| Keymap | Action |
|--------|--------|
| `<C-space>` | Show completions |
| `<C-y>` | Accept completion |
| `<C-e>` | Dismiss completions |
| `<C-b>` / `<C-f>` | Scroll docs up/down |
| `<C-l>` / `<C-h>` | Next/prev snippet placeholder |

### conform.nvim - Auto-Formatting
Auto-formats on save. Currently configured for Lua (stylua).

| Keymap | Action |
|--------|--------|
| `<leader>f` | Format buffer |

### gitsigns.nvim - Git Integration
Shows added/changed/deleted lines in the gutter.

| Keymap | Action |
|--------|--------|
| `]c` | Next git change |
| `[c` | Previous git change |
| `<leader>hs` | Stage hunk |
| `<leader>hr` | Reset hunk |
| `<leader>hS` | Stage buffer |
| `<leader>hu` | Undo stage hunk |
| `<leader>hR` | Reset buffer |
| `<leader>hp` | Preview hunk |
| `<leader>hb` | Blame line |
| `<leader>hd` | Diff against index |
| `<leader>hD` | Diff against last commit |
| `<leader>tb` | Toggle blame line |
| `<leader>tD` | Toggle deleted |

### nvim-treesitter - Syntax Highlighting
AST-based syntax highlighting for: bash, c, diff, html, lua, markdown, vim, rust, toml, ron.

### tokyonight.nvim - Color Scheme
Tokyo Night color scheme (night variant).

### todo-comments.nvim - TODO Highlighting
Highlights `TODO`, `FIXME`, `HACK`, `NOTE`, `WARN` comments in code.

| Keymap | Action |
|--------|--------|
| `]t` | Next TODO comment |
| `[t` | Previous TODO comment |

### mini.nvim - Collection of Mini Plugins

**mini.ai** - Better text objects:
- `a)`, `a]`, `a}`, `a'`, `a"` - Around brackets/quotes
- `i)`, `i]`, etc. - Inside brackets/quotes

**mini.surround** - Surround operations:
- `sa` - Add surrounding (e.g. `saiw)` to surround word with parens)
- `sd` - Delete surrounding
- `sr` - Replace surrounding

**mini.statusline** - Status line showing mode, file, location, LSP diagnostics.

### guess-indent.nvim - Auto-detect Indentation
Automatically detects whether a file uses tabs or spaces and how many.

---

## Kickstart Optional Plugins (enabled)

### indent-blankline.nvim - Indent Guides
Shows vertical lines at each indentation level.

### nvim-autopairs - Auto-close Brackets
Automatically inserts closing brackets, quotes, and parentheses.

---

## Rust/Bevy Plugins (custom)

### rustaceanvim - Rust Development
Full Rust IDE experience powered by rust-analyzer. Bevy-optimized with proc macro support, clippy diagnostics, and all features enabled.

| Keymap | Context | Action |
|--------|---------|--------|
| `<leader>ra` | Rust files | Code action (grouped by rust-analyzer) |
| `<leader>rd` | Rust files | List debuggable targets |
| `<leader>rr` | Rust files | List runnable targets |
| `<leader>rt` | Rust files | List testable targets |
| `<leader>rm` | Rust files | Expand macro under cursor |
| `<leader>rc` | Rust files | Open Cargo.toml |
| `<leader>rp` | Rust files | Go to parent module |
| `<leader>rj` | Rust files | Join lines (Rust-aware) |
| `<leader>re` | Rust files | Explain error under cursor |
| `K` | Rust files | Hover actions (with action picker) |

**Bevy-specific settings:**
- `cargo.allFeatures = true` - resolves all Bevy feature-gated types
- `procMacro.enable = true` - expands `#[derive(Component)]`, `#[derive(Bundle)]`, etc.
- `check.command = clippy` - richer linting than plain `cargo check`
- Auto-import and postfix completions enabled
- Inlay hints for types, parameters, chaining, closing braces

### crates.nvim - Cargo.toml Management
Inline version info and dependency management when editing Cargo.toml.

| Keymap | Context | Action |
|--------|---------|--------|
| `<leader>ct` | Cargo.toml | Toggle crate info overlay |
| `<leader>cr` | Cargo.toml | Reload crate data |
| `<leader>cv` | Cargo.toml | Show available versions popup |
| `<leader>cf` | Cargo.toml | Show crate features popup |
| `<leader>cd` | Cargo.toml | Show dependencies popup |
| `<leader>cu` | Cargo.toml | Update crate (semver compatible) |
| `<leader>ca` | Cargo.toml | Update all crates |
| `<leader>cU` | Cargo.toml | Upgrade crate (breaking) |
| `<leader>cA` | Cargo.toml | Upgrade all crates (breaking) |
| `<leader>cH` | Cargo.toml | Open crate homepage |
| `<leader>cR` | Cargo.toml | Open crate repository |
| `<leader>cD` | Cargo.toml | Open crate documentation |
| `<leader>cC` | Cargo.toml | Open crates.io page |

### nvim-dap + nvim-dap-ui - Debugger
Step-through debugging for Rust via codelldb. Debug UI auto-opens on debug start.

| Keymap | Action |
|--------|--------|
| `<F5>` | Start / Continue |
| `<F1>` | Step Into |
| `<F2>` | Step Over |
| `<F3>` | Step Out |
| `<F7>` | Toggle Debug UI |
| `<leader>db` | Toggle breakpoint |
| `<leader>dB` | Conditional breakpoint |
| `<leader>dl` | Log point |
| `<leader>dr` | Open debug REPL |

**Tip:** Use `<leader>rd` in a Rust file to pick a debug target via rustaceanvim, which auto-configures DAP.

---

## General QoL Plugins (custom)

### oil.nvim - File Explorer
Browse and edit your filesystem like a buffer. Rename files with normal vim editing, delete with `dd`, create files by typing new names.

| Keymap | Context | Action |
|--------|---------|--------|
| `-` | Any buffer | Open parent directory |
| `<CR>` | Oil buffer | Open file/directory |
| `-` | Oil buffer | Go up one directory |
| `<C-s>` | Oil buffer | Open in vertical split |
| `<C-h>` | Oil buffer | Open in horizontal split |
| `<C-t>` | Oil buffer | Open in new tab |
| `<C-p>` | Oil buffer | Preview file |
| `<C-c>` | Oil buffer | Close oil |
| `<C-l>` | Oil buffer | Refresh |
| `g.` | Oil buffer | Toggle hidden files |
| `gs` | Oil buffer | Change sort |
| `g?` | Oil buffer | Show help |

---

## Installed Tools (via Mason)

| Tool | Purpose |
|------|---------|
| lua-language-server | Lua LSP |
| stylua | Lua formatter |
| codelldb | Rust/C/C++ debug adapter |
| rust-analyzer | Rust LSP (managed by rustaceanvim) |
