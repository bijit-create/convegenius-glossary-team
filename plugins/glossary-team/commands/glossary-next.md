---
description: Take the next chapter from the shared glossary queue and extract its terms with Haiku page readers
argument-hint: <your name or email> [number of chapters, default 1]
allowed-tools: Read, Write, Glob, Agent, Bash, PowerShell
---

You run one turn of the ConveGenius glossary team work. Follow these steps exactly. Keep your messages to the member short.

**Arguments:** $ARGUMENTS
The first word is the member (name or email). The optional second word is how many chapters to do in a row (default 1, at most 5). If no member is given, ask for it once and wait.

## 1. Find the team folder
The working folder of this session must be the shared Google Drive folder `Glossary_team`. Check that `queue.json`, `instructions/prompt_page.md` and the folder `inputs` exist in it. Use its full path as TEAM from here on.
If they are missing, stop and tell the member: "Open a new Claude session on the Google Drive folder Glossary_team (add it to My Drive as a shortcut first; on Windows it is then usually G:\My Drive\Glossary_team), then run /glossary-team:glossary-next again. Setup steps: https://github.com/bijit-create/convegenius-glossary-team"

## 2. Get the current time
Run one command to get the current UTC time:
- Windows (PowerShell): `(Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")`
- Mac (Bash): `date -u +%Y-%m-%dT%H:%M:%SZ`
Call it NOW. Use it for every time stamp below.

## 3. Pick the next chapter
Read `TEAM/queue.json`. Go through `units` in the order listed and take the first unit that meets all of these:
- its `status` is not "imported";
- `TEAM/results/<id>/report.json` does not exist;
- `TEAM/claims/<id>.json` does not exist, or its `expires_at` is earlier than NOW, or its `member` is this member.
If no unit is left, tell the member "The queue is empty. Thank you!" and stop.

## 4. Claim it
Write `TEAM/claims/<id>.json`:
`{"id": "<id>", "member": "<member>", "claimed_at": "<NOW>", "expires_at": "<NOW + 6 hours>"}`
Tell the member in one line which chapter they have (language, grade, chapter, number of pages).

## 5. Read the pages
Read `TEAM/inputs/<id>/unit.json`. Its `pages` list gives the page numbers. Use Glob on `TEAM/results/<id>/p*.json` and leave out pages that already have a `pNNN.json` (pages saved earlier, or redone pages).
Split the remaining pages into groups of 5. Start one **glossary-team:glossary-page-reader** agent per group, with model **haiku**, up to 4 at a time as separate subagents in parallel (all in one message), and wait for them. Give each agent exactly:
```
TEAM: <full path of TEAM>
CHAPTER: <id>
LANGUAGE: <language of the unit>
PAGES: <the page numbers of its group>
```
Do not read the page images or write page results yourself.

## 6. Check
Glob `TEAM/results/<id>/p*.json` again. For pages still missing, start one more glossary-team:glossary-page-reader agent (model haiku) for them, once only.
If more than 1 page in 10 is still missing, do not write a report. Tell the member which pages failed and stop; the chapter goes back to the queue when the claim expires.

## 7. Report
Get the current time again (step 2) as FINISHED. Write `TEAM/results/<id>/report.json`:
`{"id": "<id>", "member": "<member>", "model": "claude-haiku", "plugin": "glossary-team 0.3.1", "started_at": "<NOW from step 2>", "finished_at": "<FINISHED>", "pages": <number of pages in unit.json>, "pages_written": <number of pNNN.json files>, "pages_missing": [<page numbers>]}`
Tell the member: "Done: <id>, <pages_written> pages. Results are in Drive; the glossary picks them up from there."

If the member asked for more than one chapter, go back to step 2 for the next one.
