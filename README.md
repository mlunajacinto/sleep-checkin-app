# sleep-checkin-app

I spent time researching my own habits and best practices for how I function — sleep, energy, consistency — and loaded all of it into Google's NotebookLM to use as a personal daily assistant. The problem: NotebookLM has no logging features. I couldn't feed it an accurate, structured account of my day without it being tedious or incomplete.

This app solves that. It's a personal daily logging tool with two modes: log as you go throughout the day, or do a single nightly log before bed. Either way, it formats your answers into a clean log you can paste directly into NotebookLM.

---

## How it works

The pipeline is simple:

1. The app asks a predetermined set of questions
2. You answer them — either incrementally as the day goes on (log-as-you-go), or all at once at the end of the day (nightly log)
3. Log-as-you-go entries save and accumulate until you finalize your nightly log
4. Finalizing formats everything into a structured log and copies it to your clipboard
5. Paste it into NotebookLM — it handles the rest

---

## Why I built it

NotebookLM is good at synthesizing information and supporting decisions when it has context. But it has no way to receive a daily log — you'd have to type a coherent summary from scratch every night, which doesn't happen consistently. This app removes that friction. The log goes out whether you have two minutes or twenty.

---

## Running it

Open `index.html` in your browser. No install, no dependencies. It runs locally.

---

## Usage

- **Log as you go:** Answer questions throughout the day as things happen. Entries save automatically.
- **Nightly log:** Answer the full question set at the end of the day.
- **Finalize:** Combines everything into a formatted log and copies it to your clipboard.
- **Paste into NotebookLM** to update your assistant with the day's data.
