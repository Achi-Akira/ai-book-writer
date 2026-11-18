---
name: ai-book-writer
description: Write a complete multi-chapter book, novel or long story from a premise, using a staged workflow (story arc, world building, chapter outline, then write, edit and track continuity chapter by chapter) and saving each chapter to files. Use when the user asks to write a book, novel, novella or long serialized story, to outline a book into chapters, or to run or configure the AutoGen Book Generator (ai-book-writer) Python project.
---

# AI Book Writer

This skill turns a premise into a finished book by playing the six roles of the AutoGen Book Generator one after another. Work in stages, save every stage to disk, and never move to the next chapter until the current one is final.

## Before you start

Get these from the user. Ask only for what is missing, and offer sensible defaults:

- **Premise**: plot idea, key storylines, setting.
- **Characters**: names, roles, traits.
- **Style**: point of view, tense, tone, genre, reading level.
- **Length**: number of chapters (default 10) and words per chapter (default about 3,000; the original project uses 5,000).
- **Output folder**: default `book_output/`.

For long books, confirm the outline with the user before writing chapters.

## Stage 1: Story planner

Create the high-level arc and save it to `book_output/story_arc.md`:

```
STORY_ARC:
- Major Plot Points: each event that drives the story
- Character Arcs: the development path of each main character
- Story Beats: key emotional and narrative moments, in order
- Key Transitions: major shifts in direction or tone
```

Be specific. Never leave placeholders.

## Stage 2: World builder

Describe every location the arc needs and save it to `book_output/world.md`:

```
WORLD_ELEMENTS:

[LOCATION NAME]:
- Physical Description:
- Atmosphere: mood, time of day, lighting
- Key Features: important objects, layout
- Sensory Details: sights, sounds, smells

[RECURRING ELEMENTS]: settings that return, and how they change
[TRANSITIONS]: how locations connect and how characters move between them
```

## Stage 3: Outline creator

Write one entry per chapter in exactly this format, and save the whole outline to `book_output/outline.txt`:

```
Chapter 1: [Title]
Chapter Title: [Same title]
Key Events:
- [Event 1]
- [Event 2]
- [Event 3]
Character Developments: [specific moments and changes]
Setting: [specific location and atmosphere]
Tone: [emotional and narrative tone]
```

Every chapter needs every field and at least three specific key events.

## Stage 4: Chapter loop

Repeat for each chapter, in order:

1. **Memory keeper (before writing).** Read `book_output/memory.md` (create it at chapter 1) and the outline entry for this chapter. Note anything the chapter must stay consistent with.
2. **Writer.** Draft the full chapter: follow the outlined key events, keep character voices consistent, use the world details, give the scene a real ending, and reach the target word count. Do not summarize scenes that should be shown.
3. **Editor.** Review the draft against the outline, characters and world. Fix the problems directly rather than only listing them. If the chapter is short of the target length, expand it before going on.
4. **Save** the final text to `book_output/chapter_NN.txt` (two-digit number), starting with `Chapter N: Title`.
5. **Memory keeper (after writing).** Append to `book_output/memory.md`:

   ```
   MEMORY UPDATE: Chapter N
   EVENT: key events that happened
   CHARACTER: how characters changed, what they know now
   WORLD: new or changed world details
   CONTINUITY ALERT: any contradiction to fix later
   ```

Long chapters may not fit in one response. Write them in parts, appending to the chapter file, and finish the chapter before moving on. After each chapter, report progress in one line (chapter, title, word count).

## Stage 5: Finish

- Resolve any open `CONTINUITY ALERT` entries.
- Optionally join the chapters into `book_output/book.md` with a title page and table of contents.
- Tell the user where the files are, the total word count, and anything left open.

## Running the original Python project

If the user wants the AutoGen program in this repository itself rather than Claude writing the book:

```bash
python -m venv venv
venv\Scripts\activate        # macOS/Linux: source venv/bin/activate
pip install -r requirements.txt
python main.py
```

- The premise and chapter count live in `main.py` (`initial_prompt`, `num_chapters`).
- The model is set in `config.py`. By default it calls a local OpenAI-compatible server at `http://localhost:1234/v1` (for example LM Studio) with the model `Mistral-Nemo-Instruct-2407`. Change `model`, `base_url` and `api_key` to use another endpoint.
- Output goes to `book_output/` (`outline.txt`, `chapter_01.txt`, ...). Create the folder first if `main.py` fails to open `book_output/outline.txt`.
