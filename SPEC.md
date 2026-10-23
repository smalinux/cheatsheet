# Cheatsheet spec

Goal: find any fact in seconds, without leaving nvim, and read it cleanly.
Every rule below serves one of two things: **search finds it** or **eyes read it fast**.

## 1. Where files live: shelves and sheets

```
/src/cheatsheet/
  SPEC.md
  binaryninja/      <- shelf (one subject area)
    binaryninja.sheet   <- its one sheet, named after the shelf
  gdb/
    gdb.sheet
```

- **Shelf** = a directory = one subject area. Search can be limited to one shelf,
  so Python and C++ results never mix.
- **Sheet** = one file. Each shelf has **one big sheet**, `shelf/shelf.sheet`. No splitting.
- Topics inside the sheet are parts, separated by a comment line (never indexed):
  `; ===============  TOPIC`
- Section names (`## name`) must be unique in the whole sheet: prefix with the topic if needed.
- Names: lowercase, `-` between words, the word you would type to find it.
- Extension `.sheet`, plain UTF-8 text. Not markdown: it must look clean as raw text in nvim.

## 2. File layout

```
title: git
url:   https://git-scm.com/docs
tags:  vcs version-control

; ===============  branches
## branches
git switch -c NAME          # create a branch and switch to it  @new <https://git-scm.com/docs/git-switch>
git branch -d NAME          # delete a merged branch  @remove
git branch -D NAME          # delete a branch even if not merged  @remove @force

## undo
git restore FILE            # throw away my changes in FILE  @discard @revert
git reset --soft HEAD~1     # undo last commit, keep the changes staged
```

1. **Header**: the first lines, `key: value`, until the first blank line.
   - `title` (required): shown in results.
   - `url` (optional): the main docs for the whole sheet, used when an entry has no link.
   - `tags` (optional): extra search words for the whole sheet.
2. **Sections**: `## name`. They group entries, show up in results, and can be jumped to.
3. **Entries**: everything else (see 3).
4. A line starting with `;` is a comment. It is never indexed.
5. **Topic separator**: `; ===============  xyz` (`;`, 15 `=`, two spaces, topic name).
   It starts a new topic part. Topic parts hold sections, and sections hold entries.

## 3. The entry line (the core rule)

```
KEY    # DESCRIPTION    @tag @tag    <LINK>
```

| Part | Required | Rule |
|---|---|---|
| KEY | yes | the command, function, variable or term: exactly what you would copy |
| `# DESCRIPTION` | yes | ` # ` (space, hash, space) starts it. Your own words, starting with a verb |
| `@tag` | no | extra search words: synonyms, words you might misspell, the error text |
| `<LINK>` | no | where the original, readable docs are (see 4) |

Rules:

- **One fact per line, and the line must stand alone.** A search hit is the whole answer.
  No "same as above", no "see below".
- Write the description with the words you will search for later.
  If you would think "delete", write "delete" or add `@delete`.
- Placeholders are UPPERCASE: `NAME`, `FILE`, `PID`.
- Keep a line under ~120 characters before the link.
- Align the `#` column inside a section. This is optional but makes it easier to read.
- A literal ` # ` inside KEY is written `\#`.
- No tables, bold, nested lists or HTML. Nothing that needs rendering.

**Longer examples** (code, struct layouts, multi-line commands) go in an indented block
right under their entry. The block belongs to that entry and is shown with it, but
only the entry line is matched:

```
module_init(FN)             # register the function the kernel calls on insmod  @load
    #include <linux/module.h>
    static int __init hello_init(void) { pr_info("hi\n"); return 0; }
    module_init(hello_init);
```

## 4. Links: getting to the readable source

The link is how you leave the short note for the full, readable docs.

| Link form | Opens |
|---|---|
| `<https://...>` | the web page in the browser |
| `<man:memcmp(3)>` | the man page inside nvim (`:Man 3 memcmp`) |
| `<doc:Qt/QString::arg>` | the entry in an offline docset (Zeal), see 5 |
| `<file:~/path/to/file#L42>` | a local file, at that line |

- Prefer a link straight to the exact item (`...#arg`), not the docs home page.
- No link on the entry: the sheet's `url:` is used.

## 5. Cheatsheets and API docs are different things

- **Cheatsheets** (this spec): your short notes, in your words, hand-written.
- **API docs** (Qt, C++, Python libraries): too big to copy. They stay in their
  original form (docsets, man pages, websites), are searched as their own shelves,
  and your sheets point to them with `doc:` / `man:` / `https:` links.

Never paste API reference pages into a sheet. Write the one line you need and link the rest.

## 6. What the search tool can rely on

These are guarantees for the nvim tool, built in a later section:

- Every non-blank, non-comment, non-indented line outside the header is an entry.
- Entry ID = `shelf/sheet/section/KEY`: stable, used to jump and to de-duplicate.
- Search fields: KEY, DESCRIPTION, tags, section, sheet title and sheet tags.
- Scopes: everything, one shelf, one sheet.
- Fields are found with plain text rules (` # `, ` @`, `<...>` at the end), so
  `rg`, Telescope and fzf work on the raw files with no build step.

## 7. Checklist for a new entry

1. Is it in the right shelf, under the right topic part?
2. Does the line make sense alone?
3. Does it contain the words I will search for? If not, add `@tags`.
4. Is there a link to the full docs for when I need more?
