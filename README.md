# 🧠 Agent Coding Team (LangChain + LangGraph)

A self-healing **multi-agent coding pipeline** that turns a plain-English app idea into **working Python code**, **runs it**, and **auto-repairs errors** in a feedback loop. Built with **LangChain** (prompting), **LangGraph** (agent orchestration), and a **Gradio** UI for one-click runs.

---

## 🚀 Features

- **Architect** — converts your idea into a concise implementation plan (steps, minimal API, acceptance checks).  
- **Coder** — generates a single-file Python implementation from the plan.  
- **Runner** — executes the generated code in a temp file, capturing `stdout` / `stderr`.  
- **Reviewer** — reads any traceback and returns a **fixed, full-file** version. Loops until success or a pass limit.

**Outputs:** plan, draft code, final code, run stdout/stderr, success flag, and number of review passes.

---

## 📦 Dependencies

Make sure you install the required packages and set your API key:

```bash
pip install -U langchain langchain-openai langgraph gradio
```

```bash
# In your shell
export OPENAI_API_KEY="sk-..."
```

---

## 📁 Usage

Open and run `Agent_coding_app_LangChain.ipynb` (locally or in Colab).

The notebook exposes a simple UI:
- Paste your **App idea / coding problem**  
- Tune **model**, **temperature**, **review passes**, **timeout**  
- Inspect **Plan**, **Draft**, **Final**, **Stdout/Stderr**, and **Meta** (success + pass count)

### Programmatic usage

```python
APP_IDEA = "Build a CLI that converts a CSV to JSON with selectable columns and basic validation."

# Provided by the notebook
state = build_with_team(APP_IDEA)

print(state["plan"])         # planning artifact
print(state["draft_code"])   # first code pass
print(state["code"])         # final (possibly reviewed) code
print(state["run_stdout"])   # program output
print(state["run_stderr"])   # errors / tracebacks (if any)
print(state["success"])      # True/False
print(state["passes"])       # number of review passes used
```

---

## 🧰 Core Components

### How it works
1. **Planning** — The Architect prompt turns your idea into:
   - Overview  
   - Numbered implementation steps  
   - Minimal API / interface  
   - Acceptance checks  
2. **Coding** — The Coder prompt implements the plan as **one Python file**.  
3. **Execution** — Code is written to a temp `.py` and executed via `subprocess.run(...)` with a timeout, capturing `stdout`/`stderr`.  
4. **Review Loop** — On failure, the Reviewer consumes the traceback and emits a **full corrected file**. The graph re-runs until success or `MAX_REVIEW_PASSES` is reached.

---

## 🧪 Example Use Cases

- Rapidly prototype CLI tools and small utilities from a sentence or two.  
- Get **executable code** plus an automatic **debug/repair loop**.  
- Inspect each stage’s artifacts for learning or auditing.  
- Example idea: “Build a CLI that converts a CSV to JSON with selectable columns and basic validation.”

---

## 📌 Notes

- The Reviewer always outputs **one fenced Python code block** containing the **entire corrected file**.  
- Code executes locally via `subprocess` in a temp file. Review outputs before running in sensitive environments.  
- Best for small, self-contained Python programs (no dependency management inside the run step).  
- Requires a valid **OPENAI_API_KEY**.

---

## 🧱 Tech Stack

- Python  
- LangChain (`langchain`, `langchain-openai`)  
- **LangGraph** for graph-based agent control  
- **OpenAI** chat model (default `gpt-4o-mini`, configurable)  
- **Gradio** for the web UI

---

## ⚙️ Configure (inside the notebook)

- `MODEL` — default: `gpt-4o-mini`  
- `TEMPERATURE` — default: `0.2`  
- `MAX_REVIEW_PASSES` — default: `3`  
- `RUN_TIMEOUT_SECS` — default: `15`

---

## 📁 Project Structure

```
.
├─ Agent_coding_app_LangChain.ipynb   # notebook with agents, graph, and UI
└─ README.md                          # this file
```

---

## 🛠️ Troubleshooting

- **Auth errors** → Ensure `OPENAI_API_KEY` is set in your shell/session.  
- **Timeouts** → Increase `RUN_TIMEOUT_SECS` for long-running programs.  
- **Infinite fix loops** → Lower complexity of the idea or raise `MAX_REVIEW_PASSES`.

---

## 📜 License

MIT — see `LICENSE` (or update to your preferred license).

---

## 🙏 Acknowledgements

Thanks to the LangChain and LangGraph communities for their excellent tooling and examples.
