# START HERE — MP2 "Ask Your Documents" (ready to run)

The whole project is built and tested offline. Two things are left that only you can do, because they need your OpenAI
key and your own words: **run it against the real services**, and **write the reflection**. Nothing in the folder is a
fabricated result: there is no `mp2_validation.txt` yet and the reflection is a template.

## Inside `mp2-ask-your-documents/` (this is the repo you push)

* `mp2_rag.py`: the pipeline (the 7 functions) and CLI. `mp2_hybrid.py`: optional BM25 + RRF stretch.
* `data/learner_questions.jsonl`: three questions I wrote for you (easy, medium, hard, across three different stories).
  They are yours to keep or replace; if you swap them, run validate again.
* `mp2_reflection.md`: the four sections, with `<<FILL ...>>` prompts. `check_submission.py`: pre-push checklist.
* `tests/`: over 50 offline tests (`python -m pytest -q`). `README.md`: design decisions for a reviewer.

## Steps (about 30–45 minutes of your time, plus the reflection)

1. **Set up** (PowerShell, inside the repo folder):
   `python -m venv .venv` → `.\.venv\Scripts\Activate.ps1` → `pip install -r requirements.txt`
2. **Configure:** `copy .env.example .env`, open `.env` in Notepad and set `OPENAI_API_KEY`. For Qdrant pick one:
   * *Easiest:* delete the two `QDRANT_` lines. The script then uses Qdrant's embedded on-disk mode (same engine, no
     account). Say so in your reflection, since the brief mentions a Qdrant URL.
   * *Qdrant Cloud:* set `QDRANT_URL` and `QDRANT_API_KEY` (a free cluster can take ~60 s to wake up).
   * *Docker:* `QDRANT_URL="http://localhost:6333"` and remove the API key line.
   No `source .env` is needed on Windows; the script reads the file itself.
3. **Look at the chunks** (no key needed): `python mp2_rag.py chunks`
4. **Ingest:** `python mp2_rag.py ingest` — expect `76 chunks stored`.
5. **Validate and save the evidence:** `python mp2_rag.py validate --save` — this prints the results for all 5 questions
   and writes `mp2_validation.txt` (UTF-8). Aim for `Source-match: 2/2` and `3/3`.
6. **If a question fails:** `python mp2_rag.py inspect "your question"` shows exactly which chunks were retrieved and
   their scores. That is the material for the "What didn't work" section.
7. **Optional stretch:** `python mp2_rag.py compare` prints dense versus hybrid ranks. Paste the table into the last
   reflection section, or delete that section.
8. **Reflection:** fill in `mp2_reflection.md` using your real output. If you paste `mp2_validation.txt` and any
   `inspect` output into this chat, I can draft the four sections from what actually happened; rewrite the draft in your
   own voice, because the brief asks for your observations.
9. **Check:** `python check_submission.py` must say "Ready to push".
10. **Push** to a new personal GitHub repo (separate from the capstone):
    `git init` → `git add .` → `git commit -m "feat(mp2): mini-RAG over Sherlock Holmes stories"` →
    `git branch -M main` → `git remote add origin <your-repo-url>` → `git push -u origin main`, then submit the URL.
    `.gitignore` already keeps `.env` and `.qdrant_local/` out of the repo.

## If something goes wrong

* `Setup problem: ...` messages say what to fix (key, billing, Qdrant URL, sleeping cluster).
* `Collection 'mp2_sherlock' not found` → run `ingest` first (and use the same Qdrant settings for `ingest` and `ask`).
* A `✗` on validate is a retrieval miss, not a crash: use `inspect`, look at the chunks, and describe what you see.
* Never paste your API key into a chat, a file in the repo or a screenshot. If one leaks, rotate it in the OpenAI dashboard.
