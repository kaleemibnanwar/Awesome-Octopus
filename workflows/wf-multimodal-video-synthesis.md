# Automated Workflow: Multimodal Video Synthesis & Distribution 🎬🚀

> **Declarative Blueprint for Autonomous Footage Ingestion, AI Trimming, Vertical Reframing, Captioning & CDN Publishing**  
> Orchestrated via: `OctopusStudio` + `octocut` + `OctopusMCP-Manager`

---

## 📌 Workflow Summary

This workflow automates the end-to-end transformation of multi-hour raw broadcast recordings into viral, captioned, vertically reframed video clips ready for distribution across YouTube Shorts, TikTok, and Instagram Reels.

---

## 🔄 Declarative Pipeline DAG

```
               ┌─────────────────────────────────┐
               │  Trigger: New Raw Video Upload  │
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 1: Speech-to-Text & Align  │
               │ (octocut Whisper ASR Engine)    │
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 2: Semantic Highlight Find │
               │ (OctopusStudio LLM Topic Agent) │
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 3: GOP-Lossless Trimming   │
               │ (octocut Slicing Core)          │
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 4: 9:16 Face Reframe & Sub │
               │ (octocut GPU Render Worker)     │
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 5: Audited Multi-CDN Push  │
               │ (OctopusMCP-Manager Vault & API)│
               └─────────────────────────────────┘
```

---

## 📄 Declarative DAG JSON Definition

```json
{
  "$schema": "http://octopus.engine/schemas/workflow-v2.json",
  "name": "multimodal-video-synthesis",
  "version": "2.1.0",
  "concurrency": 2,
  "nodes": [
    {
      "id": "transcribe_raw_media",
      "tool": "octocut_transcribe_and_align",
      "inputs": {
        "source_file": "{{inputs.video_url}}",
        "model": "whisper-large-v3",
        "word_timestamps": true
      }
    },
    {
      "id": "identify_viral_segments",
      "agent_role": "explorer",
      "depends_on": ["transcribe_raw_media"],
      "prompt": "Analyze the aligned transcript from {{transcribe_raw_media.output}} and extract 3 high-energy segments between 30 and 60 seconds with viral hook potential."
    },
    {
      "id": "render_vertical_clips",
      "tool": "octocut_batch_render_shorts",
      "depends_on": ["identify_viral_segments"],
      "inputs": {
        "source_file": "{{inputs.video_url}}",
        "segments": "{{identify_viral_segments.output.segments}}",
        "aspect_ratio": "9:16",
        "tracking": "speaker_face",
        "caption_preset": "tiktok_pop_yellow"
      }
    },
    {
      "id": "publish_to_social_cdn",
      "tool": "mcp_gateway_dispatch",
      "depends_on": ["render_vertical_clips"],
      "inputs": {
        "mcp_server": "social-publishing-service",
        "action": "publish_shorts",
        "media_files": "{{render_vertical_clips.output.rendered_files}}",
        "metadata": "{{identify_viral_segments.output.metadata}}"
      }
    }
  ]
}
```
