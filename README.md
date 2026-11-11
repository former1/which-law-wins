# Which Law Wins? Instruction Hierarchies in LLMs

When a **system prompt**, a **user message**, **earlier conversation history** and a **tool output** give a language model contradictory instructions, which one does it follow? This project builds a benchmark to measure that, and to check whether a model's choices form a consistent priority ordering.

The project is inspired by **IHEval** (Zhang et al., NAACL 2025):
- Paper: https://arxiv.org/abs/2502.08745
- Code and data: https://github.com/ytyz1307zzh/iheval

---

## Phase One (current)

1. **Baseline.** Run 60 IHEval examples (30 Rule Following + 30 Classification) on two models, in all three settings (reference, aligned, conflict), and score them with IHEval's own code.
2. **Our own data.** Write 150 tests, each with a matching control (300 prompts in total), covering all six source matchups with 25 tests each.

Full instructions are in the **Phase One Team Handout** (in the shared Drive folder).

## Team

| Name | Technical job | Category | Reviews |
|---|---|---|---|
| Berk | IHEval examples (`baseline_ids.json`) | Rule following | Lorenzo |
| Lorenzo | Model 1: Qwen3-8B | Task execution | Andrei |
| Andrei | Model 2: Llama 3.1 8B Instruct | Conversation history | Bugra |
| Bugra | Scoring and baseline summary | Safety defence | Federico |
| Federico | Template, combining and final checks | Tool use | Berk |

The project lead coordinates and settles disagreements.

---

## Where things live

| What | Where |
|---|---|
| Code: notebooks and scripts | **This GitHub repo** |
| Tests spreadsheet, baseline IDs, model inputs and outputs, settings, scores | **Shared Google Drive folder `WhichLawWins/`** |

**Rule of thumb:** code goes in GitHub; data and results go in Drive.

### Repository layout

```
which-law-wins/
├── README.md
├── .gitignore
├── notebooks/        # Colab notebooks (clear outputs before committing)
│   ├── run_qwen3_8b.ipynb
│   └── run_llama31_8b.ipynb
└── scripts/          # Python scripts
    ├── pick_baseline_ids.py
    ├── score_baseline.py
    └── combine_tests.py
```

Create files as you need them; the names above are suggestions.

### Drive layout

```
WhichLawWins/
├── baseline/
│   ├── baseline_ids.json
│   ├── qwen3-8b/      inputs.jsonl  outputs.jsonl  settings.json
│   ├── llama3.1-8b/   inputs.jsonl  outputs.jsonl  settings.json
│   └── scores/        scores.csv  summary
└── tests/             tests_template (Google Sheet)  tests_combined.json  counts.csv
```

---

## Getting started

### Everyone
1. Accept the GitHub invite for this repo.
2. Open the shared Drive folder, then **Shared with me → right-click `WhichLawWins` → Organise → Add shortcut → My Drive**. Colab can then find it at `/content/drive/MyDrive/WhichLawWins`.
3. Read the team handout.

### Running models on Colab (Lorenzo, Andrei)
1. Open a notebook in Colab: **File → Open notebook → GitHub**, then pick this repo.
2. **Runtime → Change runtime type → GPU (T4).**
3. Create a free Hugging Face account and a **read** access token. For Llama 3.1, first accept Meta's licence on the model page.
4. In Colab, click the **key icon (Secrets)** in the left sidebar, add a secret named `HF_TOKEN`, and turn on notebook access. In code:
   ```python
   from google.colab import userdata
   hf_token = userdata.get("HF_TOKEN")
   ```
5. Mount Drive and save **each output as soon as it is generated**:
   ```python
   from google.colab import drive
   drive.mount("/content/drive")
   OUT = "/content/drive/MyDrive/WhichLawWins/baseline/qwen3-8b/"
   ```
6. Install what you need at the top of the notebook:
   ```python
   !pip install -q transformers accelerate bitsandbytes
   ```

### Shared generation settings (agreed by Lorenzo and Andrei)
- 4-bit quantisation (bitsandbytes)
- Greedy decoding (`do_sample=False`), the same as IHEval's temperature 0
- Qwen3 thinking mode **off** (`enable_thinking=False` in the chat template)
- One shared maximum output length, written in `settings.json`

Each `settings.json` records: model ID, 4-bit settings, decoding settings, maximum output length, thinking mode, date and Colab GPU type.

---

## Rules

- **Never commit secrets.** No Hugging Face tokens, API keys or `.env` files in notebooks, scripts or commits. Use Colab Secrets. If a key is ever committed, **revoke it immediately**: deleting the file is not enough, because it stays in the Git history.
- **Clear notebook outputs before committing** (**Edit → Clear all outputs**). Outputs can contain tokens and large data.
- **No model weights or large files in the repo.** Models download into Colab each session. Data and results go in Drive.
- **Keep this repo private.** IHEval is licensed CC BY-NC-ND 4.0, which restricts sharing modified versions of its data. Our tests must be our own writing.
- **Write clear commit messages**, e.g. `Add Qwen3 baseline notebook` or `Fix ID sampling seed`.

## Committing from Colab (simple way)

In Colab: **File → Save a copy in GitHub**, choose this repo and the `notebooks/` folder, write a short commit message, and untick "Include a link to Colaboratory" if you prefer.

---

## References

- Zhang, Z. et al. (2025). *IHEval: Evaluating Language Models on Following the Instruction Hierarchy.* NAACL 2025. https://arxiv.org/abs/2502.08745
- IHEval repository: https://github.com/ytyz1307zzh/iheval
