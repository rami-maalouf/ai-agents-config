---
name: obsidian-search
version: 1.0.0
description: Find and retrieve notes from Rami's Obsidian vault by topic or theme using semantic search against Smart Connections embeddings. Use when asked to find notes about a specific subject, retrieve relevant vault content, or surface what Rami has written about a topic.
when-to-trigger:
  - user asks to find notes in the Obsidian vault about a topic
  - user asks "what do I have written about X"
  - user asks to retrieve notes related to a theme before writing, brainstorming, or building on existing ideas
  - user says "look in my vault for..." or "check my notes on..."
  - user wants to connect an idea to something they've previously thought through
allowed-tools:
  - Read
  - Bash
  - Glob
  - Grep
---

# Obsidian Vault Search

You help Rami find relevant notes in his Obsidian vault using semantic search. This is NOT the same as letterly-automation (which imports notes) — this skill is purely for finding existing notes by topic.

## Vault location

```
/Users/rami/Library/Mobile Documents/iCloud~md~obsidian/Documents/Obsidian
```

Also accessible via symlink at: `/Users/rami/Documents/life-os/Obsidian`

Use the symlink path when working inside the life-os repo.

## Smart Connections embeddings

The vault uses the Smart Connections Obsidian plugin. Embeddings live in:
```
<vault>/.smart-env/multi/
```

Each `.ajson` file corresponds to one note. The filename is the note path with `/`, ` `, and `.` replaced by `_`, then `.ajson` appended.

Example: `My Notes/Foo bar.md` → `My_Notes_Foo_bar_md.ajson`

The shared utility for reading these is at:
```
/Users/rami/Documents/life-os/ai-agents-config/skills/utils/smart_connections.py
```

Key functions:
- `get_vault_root()` — finds the vault by looking for `.smart-env/`
- `load_note_embeddings()` — loads all note-level vectors
- `load_block_embeddings()` — loads all heading-level vectors

## Search approach

### Option A — Semantic search via Smart Connections (preferred when embeddings are available)

Run a semantic search using the existing utility:

```bash
cd /Users/rami/Documents/life-os
.venv/bin/python ai-agents-config/skills/obsidian-similar-notes/scripts/find_similar.py \
  --query "TOPIC HERE" \
  --mode note \
  --threshold 0.4 \
  --top 10
```

If the venv isn't set up, fall back to Option B.

### Option B — Keyword search with grep (fallback)

```bash
grep -r -l --include="*.md" -i "KEYWORD" "/Users/rami/Library/Mobile Documents/iCloud~md~obsidian/Documents/Obsidian" 2>/dev/null | head -20
```

Then read the top matches.

### Option C — Direct folder browsing

If the user specifies a folder (e.g., "check my Essays folder"), use Glob to list files in that subtree, then read the most relevant ones.

## After finding matches

1. List the matching notes with their path and a 1-sentence summary of what each is about.
2. Ask the user which ones they want to read in full, or read the top 3 automatically if they asked you to "fetch" or "retrieve" them.
3. If the user wants to build on the content (write an article, brainstorm, etc.), read the full note text and present it clearly before proceeding.

## Key vault structure (for context)

- `unprocessed/` — inbox, newly imported notes not yet organized
- `My Outputs/Transcriptions/` — processed voice memos and video transcripts
- `My Outputs/My Essays/` — published or draft essays
- `My Resources/` — Maps of Content (MOCs) only, no raw notes
- `Hidden/` — personal/private notes (gitignored, may have access)

## Notes on the embeddings model

Model: `TaylorAI/bge-micro-v2` (384-dim vectors)
Similarity threshold for "related": 0.45 (use 0.4 for broader search)
Block-level search uses heading chains: `path#H1#H2`
