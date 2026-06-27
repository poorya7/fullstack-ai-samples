# Full-Stack AI Web App Samples

This repo holds selected code samples from two production web apps I designed, built, and shipped solo: **CaptureShark** and **RecapShark**. Both are live, and both lean heavily on LLM/vision integration, real-time pipelines, and cost-controlled production infrastructure.

**A note on what's here:** these are curated excerpts meant to show how I architect and write code, not the complete apps. I keep the full source private, so some files are trimmed, dependencies and config are omitted, and the samples are not meant to run standalone. If you'd like a deeper walkthrough or a live demo of either app, I'm happy to set one up.

---

## CaptureShark — captureshark.com

Offline-first lead-capture app: photograph a paper sign-in sheet, and vision LLMs turn it into structured rows written straight to the user's Google Sheet.

**Stack:** React 19, TypeScript, Vite, Zustand, Dexie/IndexedDB, FastAPI/Python 3.12, Pydantic, OpenAI (GPT-5 vision, GPT-4o-mini, Whisper), Google OAuth/Sheets.

- Ran a rigorous vision-model evaluation (6 models, 85+ real and synthetic sheets) to pick the production model on accuracy, hallucination rate, latency, and cost-per-lead.
- Found that GPT-5 at minimal reasoning cut latency ~91% (30.7s to 2.8s p50) with no accuracy loss.
- Durable offline capture queue (state machine in IndexedDB) that survives tab, browser, and device restarts, with idempotency keys to prevent duplicate writes.
- Multi-layer cost-cap middleware (global, per-user, per-IP) that refuses before overshoot, holding cost under $1/user/month.
- Hexagonal architecture with a hot-swappable vision provider (env-var rollback, no redeploy).

---

## RecapShark — recapshark.com

Turns any YouTube video into an interactive document: AI summaries, chapters, word-synced transcript, and chat, in 100+ languages.

**Stack:** Vanilla ES6 modules, Vite, FastAPI/Python, Postgres/Supabase, OpenAI (GPT-4o-mini), Gladia (word-level timestamps), Google Translate, DigitalOcean, nginx, PM2, Sentry.

- Word-level "karaoke" transcript highlighting synced to playback, holding a locked 60 fps on 4-hour transcripts.
- Lazy-chunked transcription pipeline that captions unlimited-length audio (10-hour podcasts) past the vendor's per-job ceiling, with constant memory use.
- Cross-user cache plus a cost-control layer that cut transcription cost 50 to 70%.
- Dual-path translation (Google Translate for 85+ languages, GPT-4o fallback for low-resource scripts) with caching and spend caps.
