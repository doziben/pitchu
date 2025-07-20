## 🗣 Text-to-Speech (TTS) Layer

**Goal:**  
Convert AI-generated text responses into high-quality, low-latency audio using OpenAI's TTS API, and stream it back to the user in near real time.

---

### 🔩 Core Responsibilities

1.  **Receive Gemini Response**

    - Accept clean AI text from the **AI Interaction Layer**.
    - Bundle includes:

      - `text`: the message to convert
      - `voice`: user-selected OpenAI voice profile (e.g., `"nova"`)

2.  **Send to OpenAI TTS Endpoint**

    - Use `/v1/audio/speech`
    - Provide the following:

      - `model`: `tts-1` or `tts-1-hd` (use `tts-1` for real-time)
      - `voice`: selected voice from 11 supported
      - `input`: the text to synthesize
      - `response_format`: `"mp3"` or `"opus"` (for fast streaming)
      - `speed`: (optional) adjust if needed for tempo

3.  **Stream Audio Output**

    - Use `fetch`/`axios` stream or `ReadableStream` (Node/Edge Function)
    - Stream response directly to:

      - Client app for immediate playback
      - WebSocket (if using browser-side audio player)

4.  **Error Handling & Fallback**

    - Retry failed TTS requests (e.g., on network issues or empty responses)
    - Fallback voice if selected one fails
    - Log issues for debugging via Supabase logs or Sentry

---

### 🎚 Voice Configuration Options

From OpenAI:

> You can let the user choose from any of the 11 built-in voices:

bash

CopyEdit

`alloy, ash, ballad, coral, echo, fable, nova, onyx, sage, shimmer`

- Show samples (via OpenAI.fm or hosted locally) in the app’s voice settings.
- Save user preference to `call_sessions.voice`
- Use this when calling TTS API.

---

### ⚙️ Suggested Implementation Steps

- Create `/speak` edge function or API route.
- Input: `{ text, voice, callId, segmentId }`
- Output: streaming audio buffer
- Pipe stream:

  - Directly to client (WebSocket or fetch/stream)
  - Optionally store per chunk in Supabase Storage

---

### 📦 Optional Enhancements

- Cache recent responses if playback is paused or repeated.
- Option to **download full call audio** (concatenate chunks server-side).
- Experiment with `tts-1-hd` for post-call export (better quality).
