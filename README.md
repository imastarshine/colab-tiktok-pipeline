# colab-tiktok-pipeline

A pipeline that converts short videos (TikTok clips or any local video files) into structured knowledge notes:
`Video` → `Whisper Speech` → `Frame OCR (Tesseract)` → `Qwen AI Notes` → `Topic Digests`

These notebooks are designed for Google Colab and Google Drive. Since the project is open-source, you can easily modify the pipeline to suit your own needs.

## What the pipeline does
1. Looks for videos in `TikTokNotes/Videos/`, including nested folders.
2. Transcribes speech with Whisper and extracts video frames with FFmpeg.
3. Recognizes on-screen text with Tesseract OCR. Near-duplicate frames are deduplicated with perceptual hashes; with tile-level dedup, a static picture with changing captions (e.g. running subtitles) is still re-recognized.
4. Sends the speech and frame text to Qwen and creates a Markdown note with a flexible structure and a content-value rating.
5. Optionally (Stage 6) collects notes into per-topic digest files (`Topics/<tag>.md`), growing a knowledge base as you process more videos.

Caching is fingerprint-based (file size + mtime) per stage: unchanged videos are not reprocessed, and changed settings invalidate only the affected stage.

## About inference
1. For transcribing speech, the pipeline uses [openai/whisper-large-v3](https://huggingface.co/openai/whisper-large-v3) with batched inference enabled by default.
2. For on-screen text, the pipeline uses Tesseract OCR (`rus + eng`).
3. For generating notes, the pipeline uses [Qwen/Qwen3-14B-GGUF](https://huggingface.co/Qwen/Qwen3-14B-GGUF/blob/main/Qwen3-14B-Q4_K_M.gguf) (Q4_K_M quantization).

## Before you start
1. Select the **T4 GPU** runtime type in Colab. All settings are located at the beginning of the first cell.
2. Upload your video files to the `TikTokNotes/Videos/` folder in your Google Drive. The notebook processes videos already placed there — there is no TikTok link downloading.
3. Choose the note language: `NOTE_LANGUAGE = "ru"` (Russian prompts and notes, default) or `"en"` (English ones). Metadata and tags are always extracted in English.

## Folders
- `TikTokNotes/Videos/` — source videos.
- `TikTokNotes/Raw/` — transcribed speech.
- `TikTokNotes/FrameText/` — text recognized on frames.
- `TikTokNotes/Notes/` — generated notes. The nested `Notes/.metadata/` stores validated metadata to avoid re-requesting it if note generation fails.
- `TikTokNotes/Topics/` — per-topic digest notes (`Topics/<tag>.md` plus `INDEX.md`).
- `TikTokNotes/Cache/` — ASR and OCR caches.
- `/content/qwen3-14b-gguf/` — Qwen GGUF file (~9 GB) on Colab's local disk. It doesn't take up space on Google Drive, but is deleted after a runtime reset and must be downloaded again.

## Run in Colab
Choose the notebook you prefer: the basic version, or the extended one with topic digest generation (Stage 6):

[![Open in Colab INTL](https://img.shields.io/badge/Open_in_Colab-Basic-blue?logo=googlecolab)](https://colab.research.google.com/github/imastarshine/colab-tiktok-pipeline/blob/main/TikTok_Video_to_Notes_INTL.ipynb)
[![Open in Colab Topics](https://img.shields.io/badge/Open_in_Colab-Topics-green?logo=googlecolab)](https://colab.research.google.com/github/imastarshine/colab-tiktok-pipeline/blob/main/TikTok_Video_to_Notes_INTL_TOPIC_GEN.ipynb)
