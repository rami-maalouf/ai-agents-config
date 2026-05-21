# Smoke Test — obsidian-search

## Trigger

> "Can you please help me find notes in my Obsidian vault that revolve around building an app for rejection therapy in the Chalant Society? I believe I mentioned a few notes but I'm not sure which ones they are."

---

## Expected behavior

1. Claude identifies this as an Obsidian vault search request.
2. Runs semantic search for "rejection therapy Chalant Society app" using find_similar.py or grep fallback.
3. Returns a list like:

   ```
   Found 5 relevant notes:

   1. Hidden/chalant-society-app-ideas.md
      "Ideas for a mobile app to gamify rejection therapy challenges at UCalgary"

   2. My Outputs/Transcriptions/chalant-brainstorm-2025-03.md
      "Voice memo transcript about NFC tap-triggered rejection challenges"

   3. unprocessed/rejection-therapy-notes.md
      "Raw notes on using social discomfort as the core mechanic"

   4. My Outputs/My Essays/why-people-need-a-witness.md
      "Essay about how having someone present makes courageous acts easier"

   5. My Resources/Chalant Society MOC.md
      "Map of Content linking all Chalant Society notes"
   ```

4. Asks: "Want me to read any of these in full?"

---

## Pass criteria

- Finds at least 3 semantically relevant notes
- Doesn't just keyword-match "rejection" — surfaces thematically related notes too
- Presents paths + one-line summaries, not full file dumps
- Offers to read them rather than dumping all content unprompted
