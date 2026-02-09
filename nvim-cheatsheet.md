# Neovim Cheatsheet (for VSCode users)

Kickstart.nvim setup. Leader key: `<Space>`.
`<C-x>` = Ctrl+x, `<A-x>` = Alt+x, `<CR>` = Enter.

---

## Modes (the big difference from VSCode)

Vim is modal — keys do different things depending on which mode you're in.

| Mode | How to enter | What it's for |
|------|-------------|---------------|
| **Normal** | `Esc` or `<C-[>` | Navigate, delete, copy, paste, commands. **Default mode.** |
| **Insert** | `i`, `a`, `o`, etc. | Type text (this is what VSCode feels like) |
| **Visual** | `v`, `V`, `<C-v>` | Select text |
| **Command** | `:` | Run commands (save, quit, search-replace, etc.) |

Golden rule: **do your work in Normal mode, only drop into Insert to type text, then get out.**

---

## Entering Insert Mode

| Key | Action |
|-----|--------|
| `i` | Insert before cursor |
| `a` | Insert after cursor |
| `I` | Insert at beginning of line |
| `A` | Insert at end of line |
| `o` | Open new line below and insert |
| `O` | Open new line above and insert |
| `s` | Delete character and insert |
| `S` | Delete entire line and insert |
| `C` | Delete from cursor to end of line and insert |
| `cc` | Same as `S` |
| `ciw` | Delete word under cursor and insert |

---

## Basic Motions (Navigation)

### Character / Line

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `h` `j` `k` `l` | Left, Down, Up, Right | Arrow keys |
| `0` | Beginning of line | Home |
| `^` | First non-blank character | Home (smart) |
| `$` | End of line | End |
| `gg` | Top of file | Ctrl+Home |
| `G` | Bottom of file | Ctrl+End |
| `{number}G` | Go to line number | Ctrl+G |

### Word

| Key | Action |
|-----|--------|
| `w` | Next word start |
| `b` | Previous word start |
| `e` | Next word end |
| `W` `B` `E` | Same but WORD (whitespace-delimited) |

### Screen / Scrolling

| Key | Action |
|-----|--------|
| `<C-d>` | Scroll half page down |
| `<C-u>` | Scroll half page up |
| `<C-f>` | Scroll full page down |
| `<C-b>` | Scroll full page up |
| `zz` | Center cursor on screen |
| `zt` | Cursor to top of screen |
| `zb` | Cursor to bottom of screen |
| `H` | Move cursor to top of screen |
| `M` | Move cursor to middle of screen |
| `L` | Move cursor to bottom of screen |

### Jumping

| Key | Action |
|-----|--------|
| `%` | Jump to matching bracket `(){}[]` |
| `{` / `}` | Jump to previous/next blank line (paragraph) |
| `<C-o>` | Jump back (like browser back) |
| `<C-i>` | Jump forward |
| `gd` | Go to local definition |
| `grd` | Go to definition (LSP) |
| `grr` | Go to references (LSP) |
| `gf` | Go to file under cursor |

---

## Editing

### The Verb-Motion Pattern

Vim commands follow a grammar: **verb + motion/text-object**.

| Verb | Meaning |
|------|---------|
| `d` | Delete (cut) |
| `c` | Change (delete + enter insert mode) |
| `y` | Yank (copy) |
| `>` | Indent right |
| `<` | Indent left |
| `=` | Auto-indent |

Combine with motions:

| Command | Action |
|---------|--------|
| `dw` | Delete from cursor to next word |
| `d$` or `D` | Delete to end of line |
| `d0` | Delete to beginning of line |
| `dG` | Delete to end of file |
| `cw` | Change word (delete + insert) |
| `c$` or `C` | Change to end of line |
| `yy` | Yank (copy) entire line |
| `dd` | Delete (cut) entire line |
| `cc` | Change entire line |
| `>>` | Indent line right |
| `<<` | Indent line left |
| `==` | Auto-indent line |

### Text Objects (the superpower)

Text objects let you operate on structured chunks. Format: `{verb}{a/i}{object}`.
- `a` = "a" (includes surrounding delimiters)
- `i` = "inner" (just the contents)

| Command | Action |
|---------|--------|
| `diw` | Delete inner word |
| `daw` | Delete a word (including surrounding space) |
| `ci"` | Change inside quotes |
| `ca"` | Change around quotes (includes the quotes) |
| `di(` or `di)` | Delete inside parentheses |
| `da{` or `da}` | Delete around braces |
| `dit` | Delete inside HTML/XML tag |
| `dip` | Delete inner paragraph |
| `yiw` | Yank inner word |
| `ci[` | Change inside brackets |
| `vi(` | Visually select inside parens |

These work with any verb. `ci"` is probably the single most useful command coming from VSCode — it clears everything inside quotes and puts you in insert mode.

### Surround (mini.surround)

| Command | Action |
|---------|--------|
| `saiw)` | Surround word with `()` |
| `saiw"` | Surround word with `""` |
| `sd"` | Delete surrounding `""` |
| `sr"'` | Replace surrounding `"` with `'` |

### Single Character Edits

| Key | Action |
|-----|--------|
| `x` | Delete character under cursor |
| `r{char}` | Replace character under cursor |
| `~` | Toggle case |
| `J` | Join line below to current line |
| `.` | Repeat last edit (extremely powerful) |

---

## Copy, Paste, Undo

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `yy` | Copy line | Ctrl+C (on line) |
| `dd` | Cut line | Ctrl+X (on line) |
| `p` | Paste after cursor/below | Ctrl+V |
| `P` | Paste before cursor/above | — |
| `u` | Undo | Ctrl+Z |
| `<C-r>` | Redo | Ctrl+Shift+Z |

**Clipboard note:** Kickstart sets `clipboard = 'unnamedplus'`, so `y` and `p` already use the system clipboard. You can copy/paste between nvim and other apps without any prefix.

---

## Visual Mode (Selection)

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `v` | Character-wise selection | Click + drag |
| `V` | Line-wise selection | Ctrl+L |
| `<C-v>` | Block (column) selection | Ctrl+Shift+Alt+Arrow |
| `gv` | Re-select last selection | — |

While in visual mode:
- Use motions to extend selection (`w`, `$`, `}`, etc.)
- `o` to jump to other end of selection
- `d` / `y` / `c` to act on selection
- `>` / `<` to indent/dedent
- `U` / `u` to uppercase/lowercase
- `:s/old/new/g` to replace within selection

---

## Search & Replace

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `/pattern` | Search forward | Ctrl+F |
| `?pattern` | Search backward | — |
| `n` | Next match | F3 / Enter |
| `N` | Previous match | Shift+F3 |
| `*` | Search word under cursor (forward) | Ctrl+D (sort of) |
| `#` | Search word under cursor (backward) | — |
| `<leader>/` | Fuzzy search current buffer | Ctrl+F |
| `<leader>sg` | Live grep across project | Ctrl+Shift+F |
| `<leader>sf` | Find file by name | Ctrl+P |

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

| Key/Command | Action | VSCode equivalent |
|-------------|--------|-------------------|
| `<leader><leader>` | Find open buffers | Ctrl+Tab |
| `:bn` | Next buffer | — |
| `:bp` | Previous buffer | — |
| `:bd` | Close buffer | Ctrl+W |
| `:ls` | List all buffers | — |

### Windows (Splits)

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `<C-w>v` | Vertical split | Split editor right |
| `<C-w>s` | Horizontal split | Split editor down |
| `<C-w>h/j/k/l` | Move between splits | — |
| `<C-w>q` | Close split | — |
| `<C-w>=` | Equalize split sizes | — |
| `<C-w>_` | Maximize height | — |
| `<C-w>\|` | Maximize width | — |
| `<C-w>T` | Move split to new tab | — |

### Tabs

| Key/Command | Action |
|-------------|--------|
| `:tabnew` | New tab |
| `gt` / `gT` | Next / previous tab |
| `:tabclose` | Close tab |

---

## File Explorer (oil.nvim)

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `-` | Open parent directory | Explorer sidebar |
| `<CR>` | Open file/directory | Click |
| `g.` | Toggle hidden files | — |
| `<C-p>` | Preview file | — |
| `<C-s>` | Open in vertical split | — |

Edit filenames with normal vim editing, `dd` to delete files, type new names to create files. Save with `:w` to apply changes.

---

## Diagnostics & LSP

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `grd` | Go to definition | F12 |
| `grr` | Go to references | Shift+F12 |
| `gri` | Go to implementation | Ctrl+F12 |
| `grn` | Rename symbol | F2 |
| `gra` | Code action | Ctrl+. |
| `gO` | Document symbols | Ctrl+Shift+O |
| `K` | Hover info | Mouse hover |
| `<leader>th` | Toggle inlay hints | — |
| `]d` / `[d` | Next/previous diagnostic | F8 / Shift+F8 |
| `<leader>sd` | Search all diagnostics | — |

---

## Git (gitsigns)

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `]c` / `[c` | Next/previous change | — |
| `<leader>hp` | Preview hunk | Inline diff |
| `<leader>hs` | Stage hunk | Stage change |
| `<leader>hr` | Reset hunk | Revert change |
| `<leader>hS` | Stage buffer | Stage file |
| `<leader>hb` | Blame line | GitLens blame |
| `<leader>hd` | Diff against index | — |
| `<leader>tb` | Toggle inline blame | — |

---

## Completion (blink.cmp)

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `<C-Space>` | Trigger completions | Ctrl+Space |
| `<C-y>` | Accept completion | Tab / Enter |
| `<C-e>` | Dismiss | Esc |
| `<C-b>` / `<C-f>` | Scroll docs | — |
| `<C-l>` / `<C-h>` | Next/prev snippet field | Tab / Shift+Tab |

---

## Debugging (DAP)

| Key | Action | VSCode equivalent |
|-----|--------|-------------------|
| `<F5>` | Start/Continue | F5 |
| `<F1>` | Step Into | F11 |
| `<F2>` | Step Over | F10 |
| `<F3>` | Step Out | Shift+F11 |
| `<leader>db` | Toggle breakpoint | F9 |
| `<leader>dB` | Conditional breakpoint | — |
| `<leader>dr` | Debug REPL | Debug Console |
| `<F7>` | Toggle debug UI | — |

---

## Essential Commands

| Command | Action | VSCode equivalent |
|---------|--------|-------------------|
| `:w` | Save | Ctrl+S |
| `:q` | Quit | Close window |
| `:wq` or `ZZ` | Save and quit | — |
| `:q!` or `ZQ` | Quit without saving | — |
| `:wa` | Save all | Ctrl+Shift+S |
| `:e {file}` | Open file | — |
| `:Mason` | Manage LSP servers | Extensions sidebar |
| `:Lazy` | Manage plugins | — |
| `:checkhealth` | Diagnose issues | — |
| `<leader>f` | Format buffer | Shift+Alt+F |

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
