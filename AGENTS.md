# LLM Wiki — Agent Guide

## Setup

- **Ollama model**: `ollama pull gemma4:e2b` (used by both scripts, hardcoded as `MODEL_NAME`)
- **Dependencies**: `pip install -r requirements.txt` (langchain, langchain-core, langchain-ollama, langgraph, streamlit)
- **Obsidian vault path**: Update `OBSIDIAN_DIR` in **both** `llm_wiki_builder.py` and `llm_wiki_maintainer.py` before running

## Commands

| Action | Command |
|---|---|
| Build/rebuild wiki | `python llm_wiki_builder.py` |
| Run QA agent (web UI) | `streamlit run llm_wiki_maintainer.py` |

## Architecture notes

- **Builder** (`llm_wiki_builder.py`): Reads markdown files from `{OBSIDIAN_DIR}/Clippings/`, sends each through `ChatOllama(model="gemma4:e2b", temperature=0, format="json")` to extract summary/key_points/topics/entities, then writes a wiki under `{OBSIDIAN_DIR}/AI Wiki/`. **Deletes and regenerates the entire wiki on every run** (see `clear_generated_wiki()`).
- **Maintainer** (`llm_wiki_maintainer.py`): A Streamlit app wrapping a LangGraph agent that reads the built wiki and answers questions. Tools: `read_index`, `list_pages`, `read_page`. Uses `ChatOllama` without `format="json"`. The agent is hardcoded with `thread_id: "1"`.
- **Wiki output structure**: `sources/`, `topics/`, `entities/` directories plus `index.md`.
- **Links**: Uses Obsidian `[[wikilink]]` format and `obsidian://open?...` URIs throughout.

## Known quirks

- **`OBSIDIAN_DIR` must use a raw string** (`r"..."`) on Windows to avoid `SyntaxWarning` from backslash escapes.
- **The model may wrap JSON in markdown fences** (` ```json ... ``` `) despite `format="json"`. The builder's `invoke_json()` strips fences before `json.loads()`.
- Builder forces JSON-structured LLM output via `format="json"` param.
- Path helpers (`slugify`, `build_wiki_link`) are reused between both scripts; keep consistent if adding new pages.
- No tests present. No CI. Single commit in history.
