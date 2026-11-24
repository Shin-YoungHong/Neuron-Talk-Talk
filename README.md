# Neuron Talk-Talk: Voice AI Care Assistant

End-to-end Korean voice counseling pipeline (**ASR → LLM → TTS**) designed for cognitive check-in calls with elderly users.  
Awarded **Excellence Award (우수상)** at the **Winter AI Bootcamp**.

## Architecture
```text
User Speech (.wav)
   │
   ▼  [ASR Service :8009]
Whisper (Fine-tuned with LoRA / PEFT)
   │  Transcribed Korean Text
   ▼  [LLM Engine]
Qwen2.5-14B-Instruct (bfloat16, System Prompt Re-injection)
   │  Response Text + Intent Classification (Y/N)
   ▼  [TTS Service :8010]
Coqui TTS Speech Synthesis ──▶ Audio Output & CSV/JSONL Session Log
```

## My Role & Key Implementation
- **LLM–TTS–ASR Pipeline Integration (`merge-scenario`):** Connected local HTTP endpoints for ASR (`/transcribe`) and TTS (`/tts`) with `Qwen2.5-14B-Instruct` inference and interactive Jupyter widget UI.
- **Multi-turn System Prompt Re-injection:** To prevent instruction drift (foreign language leakage or overly long replies) over multi-turn conversations, re-injected `SYSTEM_PROMPT` immediately before the latest user message whenever `len(history) > 2`.
- **LLM-based Intent Classifier (`is_positive_response`):** Implemented a deterministic `Y/N` intent judge to branch conversation scenarios (e.g., playing archived family voice messages).
- **GPU & Logging Management:** Configured explicit GPU pinning (`CUDA_VISIBLE_DEVICES`), garbage collection (`gc.collect()`), and UTF-8-SIG CSV / JSONL session logging for reproducibility.

