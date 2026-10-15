# Glossary team plugin (ConveGenius)

Helps build the ConveGenius glossary for the Shiksha Anuvad translator. For each NCERT chapter, Claude Haiku reads the pages of the official translated textbook and records how each English term is printed. You run it once a day; the results are saved to Google Drive and imported into the glossary automatically.

For ConveGenius team members who have Claude (desktop app).

## One-time setup (5 minutes)

1. **Drive folder.** Open the shared folder [Glossary_team](https://drive.google.com/drive/folders/1Yz7st0M98OFGHMzl_CBxTRYhRE985hJI) in your browser, right-click it › **Organize › Add shortcut** › **My Drive**. Check that Google Drive for Desktop is running: the folder should then appear on your computer, usually at `G:\My Drive\Glossary_team` (Windows) or `~/Library/CloudStorage/GoogleDrive-<you>/My Drive/Glossary_team` (Mac).
2. **Install the plugin.** Open a terminal (Windows: PowerShell; Mac: Terminal) and run:
   ```
   claude plugin marketplace add bijit-create/convegenius-glossary-team
   claude plugin install glossary-team@convegenius-content-governance
   ```
   If `claude` is not found, install Claude Code first: https://code.claude.com/docs/en/setup

## Every day (about 10 minutes of Claude's time; you can do other work meanwhile)

1. In the Claude desktop app, open the **Code** tab and start a new session on the folder **Glossary_team** (from step 1).
2. Type, with your own email:
   ```
   /glossary-team:glossary-next your.name@convegenius.ai
   ```
3. Claude takes the next chapter nobody is working on, reads its pages with Haiku and saves the results. Leave the session open until it says **Done**.

To see progress: `/glossary-team:glossary-status`.

## Good to know

- You don't choose chapters. The queue is planned from the records: chapters already done are left out, and the rest are ranked by how many terms the glossary still lacks in that language per page. Pages with nothing new are skipped. A chapter you take is reserved for you for 6 hours.
- The page reading always runs on Claude Haiku, the smallest Claude model. It uses your Claude plan's usage: about 35–60k tokens per page, roughly 0.5–1M for a chapter.
- If a chapter has problems (missing pages), it goes back to the queue automatically and only the missing pages are redone.
- Progress by language and subject: the **Coverage** tab of the glossary usage sheet. Every chapter: `chapters.csv` in Glossary_team.
- Don't edit or delete files in Glossary_team by hand. Questions: Bijit Sarkar (bijit@convegenius.ai).

## What is in this repository

- `plugins/glossary-team/commands/glossary-next.md`: the steps Claude follows to take and read a chapter.
- `plugins/glossary-team/commands/glossary-status.md`: shows progress.
- `plugins/glossary-team/agents/glossary-page-reader.md`: the Haiku page reader.
- The reading instructions themselves live in Drive (`Glossary_team/instructions/prompt_page.md`), so they can be improved without reinstalling.
