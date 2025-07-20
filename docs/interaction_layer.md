### 🤖 AI Interaction Layer

**Goal:**  
Take the transcribed user input (text or audio), generate a fast, context-aware response using Gemini, and stream the result back to the app for playback.

---

### 🔩 Core Responsibilities

1.  **Context-Aware Prompt Construction**

    - Maintain session context (recent transcript chunks).
    - Construct a running prompt using:

      - Last few user turns (e.g., last 5 exchanges).
      - AI's previous replies.
      - AI client profile (e.g., “You are an aggressive SaaS sales coach”).

    - Ensure minimal latency by keeping context trimmed.

2.  **Model Request Handling**

    - Send user input (text or audio) to Gemini Flash via API.

      - If using text: send to Gemini chat endpoint.
      - If using audio: send directly to Gemini audio-in endpoint.

    - Receive structured response (text reply, optionally function calls or metadata).

3.  **Latency Optimization**

    - Use `streaming` Gemini output if available.
    - Pipe streaming text output straight to TTS as it arrives.

4.  **Response Formatting**

    - Parse Gemini response into:

      - `text_reply` (what AI said)
      - `function_call` (if needed)
      - `context_tags` (e.g., tone, intent)

    - Sanitize response before speech output.

5.  **Emit Response**

    - Emit `{ userText, aiText, aiAudio }` bundle back to:

      - Client UI (via WebSocket or Supabase Realtime)
      - Audio Playback Layer
      - Call Storage Layer (for logging + feedback later)

---

### ⚙️ Suggested Implementation Steps

- Build `/interact` endpoint (Node or Supabase Edge Function).
- Accept payload: `{ callId, userText, recentTranscript[], clientProfile }`
- Send to Gemini Flash endpoint (with streaming enabled).
- Pipe Gemini’s output into:

  - `Text → TTS → Client`
  - `Text → Realtime → UI + CallLog`

- Optimize for latency with fast abort/retry logic if needed.

---

### Optional Enhancements

- Add fallback logic to retry Gemini if model errors.
- Allow dynamic persona switching (e.g., “Switch to tough mentor mode”).
- Track Gemini token usage per call to analyze cost.
