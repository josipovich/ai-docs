# Neovim Cheatsheet (for VSCode users)

Kickstart.nvim setup. Leader key: `<Space>`.
`<C-x>` = Ctrl+x, `<A-x>` = Alt+x, `<CR>` = Enter.

---

## Modes (the big difference from VSCode)

Vim is modal — keys do different things depending on which mode you're in.

| Mode        | How to enter        | What it's for                                              |
| ----------- | ------------------- | ---------------------------------------------------------- |
| **Normal**  | `Esc` or `<C-[>`    | Navigate, delete, copy, paste, commands. **Default mode.** |
| **Insert**  | `i`, `a`, `o`, etc. | Type text (this is what VSCode feels like)                 |
| **Visual**  | `v`, `V`, `<C-v>`   | Select text                                                |
| **Command** | `:`                 | Run commands (save, quit, search-replace, etc.)            |

Golden rule: **do your work in Normal mode, only drop into Insert to type text, then get out.**

---

## Entering Insert Mode

| Key         | Action                              |
| ----------- | ----------------------------------- |
| `i` / `a`   | Insert before / after cursor        |
| `I` / `A`   | Insert at beginning / end of line   |
| `o` / `O`   | New line below / above and insert   |
| `s`         | Delete character and insert         |
| `S` or `cc` | Delete entire line and insert       |
| `C`         | Delete to end of line and insert    |
| `ciw`       | Delete word under cursor and insert |

---

## Basic Motions (Navigation)

### Character / Line

| Key             | Action                                  | VSCode equivalent        |
| --------------- | --------------------------------------- | ------------------------ |
| `h` `j` `k` `l` | Left, Down, Up, Right                   | Arrow keys               |
| `0` / `^` / `$` | Line start / first non-blank / line end | Home / Home(smart) / End |
| `gg` / `G`      | Top / bottom of file                    | Ctrl+Home / Ctrl+End     |
| `{number}G`     | Go to line number                       | Ctrl+G                   |

### Word

| Key             | Action                                       |
| --------------- | -------------------------------------------- |
| `w` / `b` / `e` | Next word start / prev word start / word end |
| `W` / `B` / `E` | Same but WORD (whitespace-delimited)         |

### Screen / Scrolling

| Key                | Action                                 |
| ------------------ | -------------------------------------- |
| `<C-d>` / `<C-u>`  | Scroll half page down / up             |
| `<C-f>` / `<C-b>`  | Scroll full page down / up             |
| `zz` / `zt` / `zb` | Center / top / bottom cursor on screen |
| `H` / `M` / `L`    | Cursor to screen top / middle / bottom |

### Jumping

| Key               | Action                                     |
| ----------------- | ------------------------------------------ |
| `%`               | Jump to matching bracket `(){}[]`          |
| `{` / `}`         | Previous / next blank line (paragraph)     |
| `<C-o>` / `<C-i>` | Jump back / forward (like browser history) |
| `gd`              | Go to local definition                     |
| `gf`              | Go to file under cursor                    |

---

## Editing

### The Verb-Motion Pattern

Vim commands follow a grammar: **verb + motion/text-object**.

| Verb      | Meaning                             |
| --------- | ----------------------------------- |
| `d`       | Delete (cut)                        |
| `c`       | Change (delete + enter insert mode) |
| `y`       | Yank (copy)                         |
| `>` / `<` | Indent right / left                 |
| `=`       | Auto-indent                         |

Combine with motions:

| Command            | Action                                 |
| ------------------ | -------------------------------------- |
| `dw`               | Delete from cursor to next word        |
| `d$` or `D`        | Delete to end of line                  |
| `d0`               | Delete to beginning of line            |
| `dG`               | Delete to end of file                  |
| `cw` / `c$` or `C` | Change word / change to end of line    |
| `yy` / `dd` / `cc` | Yank / delete / change entire line     |
| `>>` / `<<` / `==` | Indent right / left / auto-indent line |

### Text Objects (the superpower)

Text objects let you operate on structured chunks. Format: `{verb}{a/i}{object}`.

- `a` = "a" (includes surrounding delimiters)
- `i` = "inner" (just the contents)

| Command        | Action                                       |
| -------------- | -------------------------------------------- |
| `diw` / `daw`  | Delete inner word / a word (including space) |
| `ci"` / `ca"`  | Change inside / around quotes                |
| `di(` or `di)` | Delete inside parentheses                    |
| `da{` or `da}` | Delete around braces                         |
| `dit`          | Delete inside HTML/XML tag                   |
| `dip` / `yap`  | Delete inner paragraph / yank a paragraph    |
| `yiw` / `ci[`  | Yank inner word / change inside brackets     |
| `vi(`          | Visually select inside parens                |

These work with any verb. `ci"` is probably the single most useful command coming from VSCode — it clears everything inside quotes and puts you in insert mode.

### Surround (mini.surround)

| Command           | Action                           |
| ----------------- | -------------------------------- |
| `saiw)` / `saiw"` | Surround word with `()` / `""`   |
| `sd"`             | Delete surrounding `""`          |
| `sr"'`            | Replace surrounding `"` with `'` |

### Single Character Edits

| Key       | Action                                |
| --------- | ------------------------------------- |
| `x`       | Delete character under cursor         |
| `r{char}` | Replace character under cursor        |
| `~`       | Toggle case                           |
| `J`       | Join line below to current line       |
| `.`       | Repeat last edit (extremely powerful) |

---

## Copy, Paste, Undo

| Key           | Action                      | VSCode equivalent         |
| ------------- | --------------------------- | ------------------------- |
| `yy` / `dd`   | Copy / cut line             | Ctrl+C / Ctrl+X (on line) |
| `p` / `P`     | Paste after / before cursor | Ctrl+V                    |
| `u` / `<C-r>` | Undo / redo                 | Ctrl+Z / Ctrl+Shift+Z     |

**Clipboard note:** Kickstart sets `clipboard = 'unnamedplus'`, so `y` and `p` already use the system clipboard. You can copy/paste between nvim and other apps without any prefix.

---

## Visual Mode (Selection)

| Key     | Action                   | VSCode equivalent    |
| ------- | ------------------------ | -------------------- |
| `v`     | Character-wise selection | Click + drag         |
| `V`     | Line-wise selection      | Ctrl+L               |
| `<C-v>` | Block (column) selection | Ctrl+Shift+Alt+Arrow |
| `gv`    | Re-select last selection | —                    |

While in visual mode:

- Use motions to extend selection (`w`, `$`, `}`, etc.)
- `o` to jump to other end of selection
- `d` / `y` / `c` to act on selection
- `>` / `<` to indent/dedent
- `U` / `u` to uppercase/lowercase
- `:s/old/new/g` to replace within selection

---

## Search & Replace

| Key                     | Action                                      | VSCode equivalent |
| ----------------------- | ------------------------------------------- | ----------------- |
| `/pattern` / `?pattern` | Search forward / backward                   | Ctrl+F            |
| `n` / `N`               | Next / previous match                       | F3 / Shift+F3     |
| `*` / `#`               | Search word under cursor forward / backward | Ctrl+D (sort of)  |
| `<Esc>`                 | Clear search highlights                     | —                 |

For fuzzy finding see [Telescope](#telescopenvim---fuzzy-finder) below — `<leader>sf` for files, `<leader>sg` for grep, `<leader>/` for current buffer.

### Search & Replace (Command Mode)

```
:%s/old/new/g      Replace all in file
:%s/old/new/gc     Replace all with confirmation
:s/old/new/g       Replace all in current line
:%s/old/new/gi     Replace all, case insensitive
```

---

## Buffers, Windows, Tabs

Think of it this way:

- **Buffer** = an open file (like a VSCode tab, but can be hidden)
- **Window** = a visible viewport into a buffer (like a split pane)
- **Tab** = a layout of windows (like a VSCode window group)

### Buffers

| Key                | Action                        | VSCode equivalent |
| ------------------ | ----------------------------- | ----------------- |
| `<leader><leader>` | Find open buffers (Telescope) | Ctrl+Tab          |
| `:bn` / `:bp`      | Next / previous buffer        | —                 |
| `:bd`              | Close buffer                  | Ctrl+W            |
| `:ls`              | List all buffers              | —                 |

### Windows (Splits)

| Key                             | Action                      | VSCode equivalent |
| ------------------------------- | --------------------------- | ----------------- |
| `<C-w>v` / `<C-w>s`             | Vertical / horizontal split | Split editor      |
| `<C-h>` `<C-j>` `<C-k>` `<C-l>` | Move focus between splits   | —                 |
| `<C-w>q`                        | Close split                 | —                 |
| `<C-w>=`                        | Equalize split sizes        | —                 |
| `<C-w>_` / `<C-w>\|`            | Maximize height / width     | —                 |
| `<C-w>T`                        | Move split to new tab       | —                 |

### Tabs

| Key                     | Action              |
| ----------------------- | ------------------- |
| `:tabnew` / `:tabclose` | New / close tab     |
| `gt` / `gT`             | Next / previous tab |

---

## Essential Commands

| Command              | Action                     | VSCode equivalent     |
| -------------------- | -------------------------- | --------------------- |
| `:w` / `:wa`         | Save / save all            | Ctrl+S / Ctrl+Shift+S |
| `:q` / `:q!` or `ZQ` | Quit / quit without saving | Close window          |
| `:wq` or `ZZ`        | Save and quit              | —                     |
| `:e {file}`          | Open file                  | —                     |
| `<leader>f`          | Format buffer              | Shift+Alt+F           |
| `:Mason`             | Manage LSP servers         | Extensions sidebar    |
| `:Lazy`              | Manage plugins             | —                     |
| `:checkhealth`       | Diagnose issues            | —                     |

---

## Tips for VSCode Users

1. **Stop reaching for the mouse.** Everything is faster with motions once muscle memory kicks in.
2. **Think in verbs + objects.** `diw` (delete inner word), `ci"` (change inside quotes), `yap` (yank a paragraph). This composability is why vim users are fast.
3. **Use `.` religiously.** It repeats your last edit. Make a change once, then `.` to apply it again wherever needed.
4. **`<C-o>` and `<C-i>` are your browser back/forward.** Jump to a definition with `grd`, read it, then `<C-o>` to go back.
5. **Use Telescope for everything.** `<leader>sf` for files, `<leader>sg` for grep, `<leader><leader>` for buffers. It's your Ctrl+P on steroids.
6. **Press `<Space>` and wait.** Which-key will show you all available keybindings from there.
7. **`*` on a word then `cgn` + `.`** is a powerful multi-cursor replacement: search for word, change next match, repeat with `.`.
8. **Oil replaces the sidebar.** Hit `-` to browse files. It's a buffer, so all vim commands work on filenames.
9. **Marks are bookmarks.** `ma` to set mark `a`, then `'a` to jump back. Lowercase = per-file, uppercase = global.
10. **Macros for repetitive edits.** `qa` to start recording into register `a`, do your edits, `q` to stop, `@a` to replay, `10@a` to replay 10 times.

---

---

# Setup — Features & Keybinding Reference

Neovim 0.11.6 with kickstart.nvim + custom Rust/Bevy plugins.
Leader key: `<Space>`

---

## Core Plugins (kickstart.nvim defaults)

### telescope.nvim — Fuzzy Finder

Find anything instantly with fuzzy search.

| Keymap             | Action                                |
| ------------------ | ------------------------------------- |
| `<leader>sf`       | Search files                          |
| `<leader>sg`       | Search by grep (live)                 |
| `<leader>sw`       | Search current word (normal + visual) |
| `<leader>sd`       | Search diagnostics                    |
| `<leader>sr`       | Resume last search                    |
| `<leader>sh`       | Search help tags                      |
| `<leader>sk`       | Search keymaps                        |
| `<leader>sc`       | Search commands                       |
| `<leader>ss`       | Search select (telescope pickers)     |
| `<leader>s.`       | Search recent files                   |
| `<leader>sn`       | Search Neovim config files            |
| `<leader>s/`       | Search in open files (live grep)      |
| `<leader><leader>` | Find existing buffers                 |
| `<leader>/`        | Fuzzy search current buffer           |

**Inside a Telescope picker:**

| Key                             | Action                   |
| ------------------------------- | ------------------------ |
| `<CR>`                          | Open in current buffer   |
| `<C-x>`                         | Open in horizontal split |
| `<C-v>`                         | Open in vertical split   |
| `<C-t>`                         | Open in new tab          |
| `<C-/>` (insert) / `?` (normal) | Show all picker keymaps  |

### which-key.nvim — Keybinding Helper

Press any leader key and wait — a popup shows all available continuations. Groups:

- `<leader>s` — Search
- `<leader>t` — Toggle
- `<leader>h` — Git Hunk
- `<leader>r` — Rust
- `<leader>d` — Debug
- `<leader>c` — Crates (in Cargo.toml)

### nvim-lspconfig + mason.nvim — Language Server Protocol

IDE features powered by language servers. Mason auto-installs servers.

| Keymap       | Action                        |
| ------------ | ----------------------------- |
| `grd`        | Go to definition              |
| `grr`        | Go to references              |
| `gri`        | Go to implementation          |
| `grt`        | Go to type definition         |
| `grD`        | Go to declaration             |
| `grn`        | Rename symbol                 |
| `gra`        | Code action (normal + visual) |
| `gO`         | Document symbols              |
| `gW`         | Workspace symbols             |
| `K`          | Hover info                    |
| `<leader>th` | Toggle inlay hints            |

Run `:Mason` to manage installed language servers and tools.

### blink.cmp — Completion Engine

Auto-completion with snippet support (LuaSnip).

| Keymap              | Action                              |
| ------------------- | ----------------------------------- |
| `<C-Space>`         | Show completions / toggle docs      |
| `<C-n>` / `<C-p>`   | Next / previous item                |
| `<C-y>`             | **Accept selected completion**      |
| `<C-e>`             | Dismiss completions                 |
| `<C-k>`             | Toggle signature help               |
| `<Tab>` / `<S-Tab>` | Next / previous snippet placeholder |

**How accepting works:** Use `<C-n>` / `<C-p>` (or `<Up>` / `<Down>`) to highlight an item, then `<C-y>` to confirm it. This inserts the completion (and auto-imports if your LSP supports it).

### conform.nvim — Auto-Formatting

Auto-formats on save. Configured for Lua (stylua) and web languages (prettierd/prettier). Rust formatting handled by rust-analyzer.

| Keymap      | Action        |
| ----------- | ------------- |
| `<leader>f` | Format buffer |

**Prettier-formatted filetypes:** javascript, javascriptreact, typescript, typescriptreact, css, json, jsonc, yaml, html, markdown.

### gitsigns.nvim — Git Integration

Shows added/changed/deleted lines in the gutter.

| Keymap       | Action                       |
| ------------ | ---------------------------- |
| `]c` / `[c`  | Next / previous git change   |
| `<leader>hs` | Stage hunk (normal + visual) |
| `<leader>hr` | Reset hunk (normal + visual) |
| `<leader>hS` | Stage buffer                 |
| `<leader>hu` | Undo stage hunk              |
| `<leader>hR` | Reset buffer                 |
| `<leader>hp` | Preview hunk                 |
| `<leader>hb` | Blame line                   |
| `<leader>hd` | Diff against index           |
| `<leader>hD` | Diff against last commit     |
| `<leader>tb` | Toggle inline blame          |
| `<leader>tD` | Toggle show deleted          |

### nvim-treesitter — Syntax Highlighting

AST-based syntax highlighting for: bash, c, css, diff, html, javascript, json, jsonc, lua, markdown, ron, rust, toml, tsx, typescript, vim, yaml.

### tokyonight.nvim — Color Scheme

Tokyo Night color scheme (night variant).

### todo-comments.nvim — TODO Highlighting

Highlights `TODO`, `FIXME`, `HACK`, `NOTE`, `WARN` comments in code.

| Keymap      | Action                       |
| ----------- | ---------------------------- |
| `]t` / `[t` | Next / previous TODO comment |

### mini.nvim — Collection of Mini Plugins

**mini.ai** — Better text objects:

- `a)`, `a]`, `a}`, `a'`, `a"` — Around brackets/quotes
- `i)`, `i]`, etc. — Inside brackets/quotes

**mini.surround** — Surround operations:

- `sa` — Add surrounding (e.g. `saiw)` to surround word with parens)
- `sd` — Delete surrounding
- `sr` — Replace surrounding

**mini.statusline** — Status line showing mode, file, location, LSP diagnostics.

### guess-indent.nvim — Auto-detect Indentation

Automatically detects whether a file uses tabs or spaces and how many.

---

## Kickstart Optional Plugins (enabled)

### indent-blankline.nvim — Indent Guides

Shows vertical lines at each indentation level.

### nvim-autopairs — Auto-close Brackets

Automatically inserts closing brackets, quotes, and parentheses.

---

## Rust/Bevy Plugins (custom)

### rustaceanvim — Rust Development

Full Rust IDE experience powered by rust-analyzer. Bevy-optimized with proc macro support, clippy diagnostics, and all features enabled.

| Keymap       | Action                              |
| ------------ | ----------------------------------- |
| `<leader>ra` | Code action (rust-analyzer grouped) |
| `<leader>rr` | List runnable targets               |
| `<leader>rd` | List debuggable targets             |
| `<leader>rt` | List testable targets               |
| `<leader>rm` | Expand macro under cursor           |
| `<leader>rc` | Open Cargo.toml                     |
| `<leader>rp` | Go to parent module                 |
| `<leader>rj` | Join lines (Rust-aware)             |
| `<leader>re` | Explain error under cursor          |
| `K`          | Hover actions (with action picker)  |

**Bevy-specific settings:**

- `cargo.allFeatures = true` — resolves all Bevy feature-gated types
- `procMacro.enable = true` — expands `#[derive(Component)]`, `#[derive(Bundle)]`, etc.
- `check.command = clippy` — richer linting than plain `cargo check`
- Auto-import and postfix completions enabled
- Inlay hints for types, parameters, chaining, closing braces

### crates.nvim — Cargo.toml Management

Inline version info and dependency management when editing Cargo.toml.

| Keymap                                     | Action                                  |
| ------------------------------------------ | --------------------------------------- |
| `<leader>ct`                               | Toggle crate info overlay               |
| `<leader>cr`                               | Reload crate data                       |
| `<leader>cv` / `<leader>cf` / `<leader>cd` | Show versions / features / dependencies |
| `<leader>cu` / `<leader>ca`                | Update crate / all crates (semver)      |
| `<leader>cU` / `<leader>cA`                | Upgrade crate / all crates (breaking)   |
| `<leader>cH` / `<leader>cR`                | Open homepage / repository              |
| `<leader>cD` / `<leader>cC`                | Open documentation / crates.io          |

### nvim-dap + nvim-dap-ui — Debugger

Step-through debugging for Rust via codelldb. Debug UI auto-opens on debug start.

| Keymap                   | Action                 |
| ------------------------ | ---------------------- |
| `<F5>`                   | Start / Continue       |
| `<F1>` / `<F2>` / `<F3>` | Step Into / Over / Out |
| `<F7>`                   | Toggle Debug UI        |
| `<leader>db`             | Toggle breakpoint      |
| `<leader>dB`             | Conditional breakpoint |
| `<leader>dl`             | Log point              |
| `<leader>dr`             | Open debug REPL        |

**Tip:** Use `<leader>rd` in a Rust file to pick a debug target via rustaceanvim, which auto-configures DAP.

---

## General QoL Plugins (custom)

### markdown-preview.nvim — Markdown Preview

| Command                                     | Action                               |
| ------------------------------------------- | ------------------------------------ |
| `:MarkdownPreview` / `:MarkdownPreviewStop` | Start / stop live preview in browser |
| `:MarkdownPreviewToggle`                    | Toggle preview                       |

### oil.nvim — File Explorer

Browse and edit your filesystem like a buffer. Rename files with normal vim editing, delete with `dd`, create files by typing new names.

| Keymap             | Action                                        |
| ------------------ | --------------------------------------------- |
| `-`                | Open parent directory (from any buffer)       |
| `<CR>`             | Open file/directory                           |
| `-` (in Oil)       | Go up one directory                           |
| `<C-s>` / `<C-h>`  | Open in vertical / horizontal split           |
| `<C-t>`            | Open in new tab                               |
| `<C-p>`            | Preview file                                  |
| `<C-c>`            | Close oil                                     |
| `<C-l>`            | Refresh                                       |
| `g.` / `gs` / `g?` | Toggle hidden files / change sort / show help |

---

## Diagnostics & Terminal

| Keymap       | Action                                        |
| ------------ | --------------------------------------------- |
| `]d` / `[d`  | Next / previous diagnostic (auto-opens float) |
| `<leader>q`  | Open diagnostic quickfix list                 |
| `<leader>sd` | Search all diagnostics (Telescope)            |
| `<Esc><Esc>` | Exit terminal mode (in `:term`)               |

---

## Web Development (TypeScript / React / Next.js)

LSP servers and tools for working with the ti-os-dev-platform (Next.js 16 + React 19 + TypeScript 5.9).

### LSP Servers

| Server   | What it provides                                                                        |
| -------- | --------------------------------------------------------------------------------------- |
| `ts_ls`  | TypeScript/JavaScript — go-to-definition, references, rename, completions, auto-imports |
| `eslint` | Inline ESLint diagnostics + code action auto-fixes (`gra` to apply)                     |
| `cssls`  | CSS language support (CSS Modules, completions, hover info)                             |
| `jsonls` | JSON support (package.json, tsconfig.json — schema validation, completions)             |

### Workflow

| Action                | How                                                |
| --------------------- | -------------------------------------------------- |
| Go to definition      | `grd` on any symbol                                |
| Find all references   | `grr`                                              |
| Rename across files   | `grn`                                              |
| Auto-fix ESLint error | `gra` on the diagnostic → select fix               |
| Format with Prettier  | Save the file (auto) or `<leader>f` (manual)       |
| See all diagnostics   | `<leader>sd` (Telescope) or `<leader>q` (quickfix) |
| Check attached LSPs   | `:LspInfo`                                         |

---

## Installed Tools (via Mason)

| Tool                       | Purpose                               |
| -------------------------- | ------------------------------------- |
| lua-language-server        | Lua LSP                               |
| stylua                     | Lua formatter                         |
| codelldb                   | Rust/C/C++ debug adapter              |
| rust-analyzer              | Rust LSP (managed by rustaceanvim)    |
| typescript-language-server | TypeScript/JavaScript LSP             |
| eslint-lsp                 | ESLint diagnostics + code actions     |
| css-lsp                    | CSS language server                   |
| json-lsp                   | JSON language server                  |
| prettierd                  | Fast Prettier daemon (format-on-save) |
