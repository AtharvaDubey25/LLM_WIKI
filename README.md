# LLM Wiki

A local LLM-powered wiki built from your Obsidian clippings. Uses Ollama and LangChain to extract structured knowledge from raw markdown files and answer questions about them.

- `llm_wiki_builder.py` — reads markdown files from your Obsidian `Clippings` folder and generates a wiki under `AI Wiki/` with sources, topics, and entities.
- `llm_wiki_maintainer.py` — a Streamlit chat interface backed by a LangGraph agent that answers questions using the generated wiki.

# Prerequisites

1. **Ollama** — install from [ollama.com](https://ollama.com/) and pull any open-source model of your choice:

   ```bash
   ollama pull gemma4:e2b
   ```

   Any model that supports tool calling and JSON output can be used. Recommended: Gemma 4, Llama 3, Mistral, Qwen 2.5, or Phi-4.

2. **Python** — 3.10 or later. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. **Obsidian vault** — your vault must have a `Clippings/` folder containing `.md` files (e.g., from the Obsidian Web Clipper extension). The wiki will be written to `AI Wiki/` inside the same vault.

# Setup

1. Open `llm_wiki_builder.py` and `llm_wiki_maintainer.py`.
2. Set `OBSIDIAN_DIR` to your Obsidian vault path (use a raw string on Windows):

   ```python
   OBSIDIAN_DIR = Path(r"C:\Users\You\Your Vault")
   ```

3. (Optional) Change `MODEL_NAME` in both files if you pulled a different model:

   ```python
   MODEL_NAME = "gemma4:e2b"   # change to your model
   ```

# Usage

**Build the wiki** — processes every `.md` in `Clippings/`, extracts summaries, key points, topics, and entities via LLM, and writes structured pages under `AI Wiki/`.

```bash
python llm_wiki_builder.py
```

**Run the Q&A agent** — launches a Streamlit web UI where you can ask questions about your wiki content.

```bash
streamlit run llm_wiki_maintainer.py
```

The agent reads the wiki index, discovers relevant pages, reads them, and answers with citations.

# Output structure

Inside your Obsidian vault:

```
AI Wiki/
├── index.md           — full index linking all sources, topics, entities
├── sources/           — one page per source file, with summary and extracted data
├── topics/            — merged topic pages with cross-links to sources and entities
└── entities/          — merged entity pages with cross-links to sources and topics
```

All pages use Obsidian `[[wikilink]]` syntax and `obsidian://open?...` URIs for seamless navigation within Obsidian.

# Notes

- The builder **deletes and regenerates** the entire wiki on every run (see `clear_generated_wiki()`).
- The model may wrap JSON output in markdown code fences despite `format="json"` being set. The builder handles this automatically.
- `OBSIDIAN_DIR` must use a raw string (`r"..."`) on Windows to avoid backslash escape warnings.
