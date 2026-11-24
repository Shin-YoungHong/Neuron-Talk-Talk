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

## Repository Structure
```text
neuron-talk-talk-voice-ai/
├── notebooks/
│   ├── asr/                       # Whisper baseline, dataset prep, LoRA fine-tuning, ASR service
│   ├── tts/                       # Audio recording, TTS synthesis, TTS service
│   └── llm_pipeline/              # Qwen2.5 LLM chat & full ASR-LLM-TTS scenario integration
├── docs/
│   ├── final_presentation.pdf     # Project slides & architecture
│   └── award_certificate.jpg      # Winter AI Bootcamp Excellence Award
├── requirements.txt
└── README.md
```


4. fpga-verilog-stopwatch/README.md

# FPGA Digital Stopwatch (Verilog)

Modular digital stopwatch designed and verified in **Verilog HDL** for **Basic Electrical & Electronic Engineering Lab (2)**.  
Features millisecond-precision timing, lap-time display, invalid-input error handling, and visual stop-state feedback on 6-digit 7-segment displays (FND) and LEDs.

## Module Architecture
```text
Stopwatch (TOP)
├── SwitchInterface   # Debouncing & pulse generation (run/stop, reset, lap)
├── State_Machine     # Core FSM controlling run, stop, lap, and error states
├── Timer             # Counter logic generating min_cnt, sec_cnt, ms_cnt, half_sec_tick
├── ledDriver         # Status LED control synchronized with timer_en / half_sec_tick
└── segDriver         # 6-digit 7-segment display driver with lap & error rendering
```

## My Role
- **System Architecture & TOP Module (`Stopwatch.v`):** Defined inter-module wire interfaces and integrated all five hardware blocks into the top-level design.
- **Switch Interface (`SwitchInterface.v`):** Designed user input conditioning to generate clean single-cycle control pulses (`run_stop_pulse`, `reset_pulse`, `lap_pulse`).

## Key Hardware Features
- **Invalid Input Error Handling:** Detects illegal button transitions inside `State_Machine` and asserts `error_sig` to display an error indicator on the FND.
- **Stop-State Blinking Feedback:** Uses `half_sec_tick` in `segDriver` to blink the display while paused so operating state is immediately distinguishable.
- **Testbench Verification:** Verified timing and state transitions using `Stopwatch_tb.v`.
