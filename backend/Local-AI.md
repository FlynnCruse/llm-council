# Nemotron Nano 8B (Local)

NVIDIA's Llama-3.1-Nemotron-Nano-8B-v1 running locally via Ollama.

## About

A reasoning model fine-tuned by NVIDIA for:
- **Tool calling / function calling**
- **RAG (Retrieval Augmented Generation)**
- **Math & code reasoning**
- **General chat**

Based on Llama 3.1 8B Instruct. Supports 128K context length.

## Usage

ollama run nemotron

If you’ve registered additional models:

- nemotron9b: `ollama run nemotron9b`
- nemotron12b: `ollama run nemotron12b`

### Reasoning Mode

Toggle reasoning with the system prompt "detailed thinking on" or "detailed thinking off".

### Recommended Settings

- **Reasoning ON**: temperature 0.6, top_p 0.95
- **Reasoning OFF**: greedy decoding (temperature 0)

## Download

Download the GGUF weights locally:
(Already downloaded)
```bash
curl -L -o nemotron-nano.gguf "https://huggingface.co/bartowski/nvidia_Llama-3.1-Nemotron-Nano-8B-v1-GGUF/resolve/main/nvidia_Llama-3.1-Nemotron-Nano-8B-v1-Q4_K_M.gguf"
```

## Files

- Modelfile - Ollama model config
- nemotron-nano.gguf - Model weights (Q4_K_M quantization, ~4.7GB)
- nemotron-9b-v2.gguf - Optional upgrade (Q4_K_M, ~6.1GB)
- nemotron-12b-v2.gguf - Optional upgrade (Q4_K_M, ~7.1GB)

## Use with LLM Council (Local)

Run Karpathy’s LLM Council fully offline using your local Ollama models.

1) Verify Ollama and models

```bash
ollama list
```

2) Install Council deps

```bash
cd Council/llm-council
uv sync
cd frontend && npm install && cd ..
```

3) Configure local mode (.env in Council/llm-council)

```bash
cat > .env <<'EOF'
COUNCIL_PROVIDER=local
LOCAL_MODELS=nemotron,nemotron9b,nemotron12b
CHAIRMAN_LOCAL_MODEL=nemotron12b
COUNCIL_MAX_PARALLEL_LOCAL=2
COUNCIL_MEM_RESERVE_GB=6
# OLLAMA_BASE_URL=http://127.0.0.1:11434
EOF
```

4) Start servers

- Option A:

```bash
./start.sh
```

- Option B:

```bash
uv run python -m backend.main
# in a new terminal:
cd frontend && npm run dev
```

Open http://localhost:5173.

5) Smoke test via CLI (optional)

```bash
bash scripts/council.sh -m "Say hi in five words"
bash scripts/council.sh --stream -m "One fun fact about space."
```

Notes
- Ensure `LOCAL_MODELS` names match `ollama list` (omit tags like `:latest`).
- If the machine is tight on RAM, lower `LOCAL_MODELS` count or set `COUNCIL_MAX_PARALLEL_LOCAL=1–2`.
- `COUNCIL_MEM_RESERVE_GB` keeps headroom for the OS/apps; increase if needed.

## Additional Models (Optional)

### NVIDIA Nemotron Nano 9B v2

Download:

```bash
curl -L -o nemotron-9b-v2.gguf "https://huggingface.co/bartowski/nvidia_NVIDIA-Nemotron-Nano-9B-v2-GGUF/resolve/main/nvidia_NVIDIA-Nemotron-Nano-9B-v2-Q4_K_M.gguf"
```

Register with Ollama:

```bash
cat > Modelfile-9b << 'EOF'
FROM ./nemotron-9b-v2.gguf
TEMPLATE """<|begin_of_text|><|start_header_id|>system<|end_header_id|>
{{ .System }}<|eot_id|><|start_header_id|>user<|end_header_id|>
{{ .Prompt }}<|eot_id|><|start_header_id|>assistant<|end_header_id|>
"""
PARAMETER temperature 0.6
PARAMETER top_p 0.95
EOF

ollama create nemotron9b -f Modelfile-9b
```

Run:

```bash
ollama run nemotron9b "What are you?"
```

---

### NVIDIA Nemotron Nano 12B v2

Download:

```bash
curl -L -o nemotron-12b-v2.gguf "https://huggingface.co/bartowski/nvidia_NVIDIA-Nemotron-Nano-12B-v2-GGUF/resolve/main/nvidia_NVIDIA-Nemotron-Nano-12B-v2-Q4_K_M.gguf"
```

Register with Ollama:

```bash
cat > Modelfile-12b << 'EOF'
FROM ./nemotron-12b-v2.gguf
TEMPLATE """<|begin_of_text|><|start_header_id|>system<|end_header_id|>
{{ .System }}<|eot_id|><|start_header_id|>user<|end_header_id|>
{{ .Prompt }}<|eot_id|><|start_header_id|>assistant<|end_header_id|>
"""
PARAMETER temperature 0.6
PARAMETER top_p 0.95
EOF

ollama create nemotron12b -f Modelfile-12b
```

Run:

```bash
ollama run nemotron12b "What are you?"
```

## Troubleshooting

- Error: supplied file was not in GGUF format  
  This usually means the downloaded file was an HTML page, not a .gguf. Make sure you:
  - Use the Hugging Face “resolve/main/... .gguf” URL.
  - Pass -L to curl to follow redirects.
  - Verify file size is several GB (ls -lh). Re-download if it’s only KB/MB.
- zsh: command not found: llama  
  Use `ollama run ...` instead of `llama run`.

## Source

- Official: https://huggingface.co/nvidia/Llama-3.1-Nemotron-Nano-8B-v1
- GGUF: https://huggingface.co/bartowski/nvidia_Llama-3.1-Nemotron-Nano-8B-v1-GGUF
- 9B v2: https://huggingface.co/nvidia/NVIDIA-Nemotron-Nano-9B-v2
- 12B v2: https://huggingface.co/nvidia/NVIDIA-Nemotron-Nano-12B-v2

## License

NVIDIA Open Model License + Llama 3.1 Community License
