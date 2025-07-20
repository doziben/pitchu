## 📘 **About Pitchu**

**Pitchu** is a real-time AI sales call training tool that lets users practice mock sales calls with an intelligent AI agent and get feedback. It’s built to feel like a real conversation — with instant voice responses and intelligent, context-aware behavior.

We're keeping things **lean for MVP** — avoiding complexity, focusing on real-time responsiveness, and shipping quickly. No bloated infra, no over-engineering. Just one user per call, simple billing, and efficient voice interaction.

---

## 🚀 **MVP Philosophy**

- **Speed > Perfection** — fastest viable path to feedback loop.
- **Lean stack** — Supabase + Vercel + Svelte 5.
- **One clear job**: Simulate sales calls + give helpful feedback.
- Real-time voice interactions only (no batching, no delays).
- Limit: **One active call per user**.

---

## 🧠 **AI Model Selection (per flow step)**

Step

Model

Audio Input → AI Response

Gemini Flash (v2.5)

AI Text → Voice (TTS)

OpenAI TTS (GPT-4o mini, tts-1)

Post-call Feedback

Claude Haiku or OpenAI GPT-4

---

## 🛠️ **Tech Stack**

Layer

Tech

Frontend

Svelte 5 + Shadcn Svelte

Backend

Supabase (Auth, DB, Edge)

Hosting

Vercel

Voice Capture

Browser/WebRTC

TTS Streaming

OpenAI `tts-1` streaming

Call Session Storage

Supabase

Billing

Stripe + Supabase

---

## 📊 **Functionality (MVP Flow & Description)**

### 1. **Call Start**

- User clicks "Start Practice Call"
- Check active session + usage quota
- Create a new `call_session` record in Supabase

### 2. **Speech Input → Text**

- User speaks via microphone
- Audio streamed to Gemini Flash
- Transcribed and processed as intent

### 3. **AI Response**

- Gemini Flash generates real-time reply
- Streamed back to user via OpenAI TTS

### 4. **TTS Playback**

- OpenAI `tts-1` converts AI text to voice
- Streamed to client for natural back-and-forth
- Playback is instant (non-blocking)

### 5. **Call Duration Tracking**

- Duration tracked in seconds (on server + client)
- On end, update `calls` table with usage

### 6. **Post-Call Feedback**

- Entire transcript and call metadata passed to Claude or GPT
- Feedback summary generated:

  - Strengths
  - Improvement tips
  - Confidence score

### 7. **Quota & Billing**

- Each user has a quota (e.g., 30 mins/month)
- Stripe used for upgrades
- Supabase tracks usage and limits

### 8. **Transcript & History**

- All calls stored in `call_sessions` + `transcripts`
- Accessible from user dashboard

---

## ⏳ **What We're NOT Building Yet**

- No queue system or concurrent call management
- No team collaboration or call scheduling
- No manual transcripts or multi-language support
- No heavy moderation or abuse detection (yet)
