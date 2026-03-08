# study-recall-app

I built this for myself. I studied my own learning patterns, put together a personal learning profile, and found that the method that actually works for me is active recall — testing yourself on material instead of rereading it. So I built a tool that does exactly that.

You paste in a resource, write what you remember, and the app grades your recall. Missed something? Drill it. That's the loop.

---

## Why local AI

I wanted AI-powered grading but I had no real coding experience going into this, and I wasn't going to pay per-token for a personal study tool. I found Ollama — a way to run LLMs locally, for free, with comparable quality for this kind of task. No API key, no cost, no cloud dependency. The app runs entirely on your machine.

---

## The hard part: grading logic

This was the most difficult thing to build. The core question was: how do you get an LLM to grade a student's recall against a source accurately, without it being too loose or too strict?

My first instinct was prompting — load the model with a grading prompt and let it decide. That didn't hold up. A model left to freely interpret recall introduces too much noise: it would credit vague overlap, miss paraphrases, and inconsistently handle missing details.

The solution was a keypoint extraction step first. Instead of asking the model to grade a free-recall response against a raw resource, the app first extracts discrete key points from the resource, then grades the student's recall against those specific points. That gives the grader a rubric instead of a judgment call.

Even then, grading needed tighter constraints. I ended up building a sequence of gates the model has to pass before a response gets marked Correct:

- **Evidence** — the match has to be supported by the resource, not inferred
- **Mention** — if the student didn't address a concept, it's Missing or Kind-of, not Correct
- **Specificity** — vague overlap doesn't count
- **Critical detail** — numbers, percentages, ranges matter; wrong or missing = not Correct
- **Contradiction** — stating the opposite of the resource is wrong, even if it's close
- **Attribution** — a key point should match the student's actual sentence, not just any sentence
- **Paraphrase** — valid paraphrasing counts as Correct; exact wording isn't required
- **Fallback** — if the model is weak, ground rules keep grading functional

The grader isn't perfect, but it's consistent and strict by design. It can't just freely decide — it has to pass all the gates.

---

## Getting started

```bash
npm install
cp .env.example .env.local
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

---

## Ollama setup

Install [Ollama](https://ollama.com), then pull the tested models:

```bash
ollama pull deepseek-r1:1.5b
ollama pull nomic-embed-text
ollama serve
```

- **Minimum chat model:** `deepseek-r1:1.5b`
- **Recommended embedding model:** `nomic-embed-text`

If you have stronger hardware, you can swap in a larger model via `OLLAMA_MODEL` in `.env.local`. Grading quality improves — app logic stays the same.

---

## Environment variables

| Variable | Default | Purpose |
|---|---|---|
| `OLLAMA_BASE_URL` | `http://localhost:11434` | Local Ollama endpoint |
| `OLLAMA_MODEL` | `deepseek-r1:1.5b` | Chat/grading model |
| `OLLAMA_EMBED_MODEL` | `nomic-embed-text` | Embedding model for semantic matching |
| `OLLAMA_NUM_CTX` | `4096` | Context window |
| `OLLAMA_TEMPERATURE` | `0` | Deterministic output |

**Embedding fallback:** If `OLLAMA_EMBED_MODEL` is unavailable, the app falls back to lexical matching. Grading still works — paraphrase detection just becomes less accurate.

---

## Workflow

1. Add a resource in `/resource`
2. Study it outside the app
3. Type your recall in `/recall`
4. Run the check — results come back as Missing / Kind-of / Correct
5. Drill your misses

---

## Validation

```bash
npm run preflight       # lint + build + regression checks
npm run eval:regression # acceptance harness only
```

---

## Troubleshooting

- **Ollama not reachable:** check `ollama serve`, verify `OLLAMA_BASE_URL`, test with `curl http://localhost:11434/api/tags`
- **Wrong model tag:** run `ollama list` and set exact installed tags in `.env.local`
- **Embedding unavailable:** app falls back to lexical matching automatically
