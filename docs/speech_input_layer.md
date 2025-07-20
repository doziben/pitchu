### 🎙 Speech Input Layer

**Goal:**  
Capture user speech, detect pauses or natural endpoints, convert to text (transcription), and forward to the AI Interaction Layer as a chunked query.

---

### 🔩 Core Responsibilities

1.  **Audio Recording**

    - Start/stop audio recording based on user activity or manual control.
    - Use Web APIs (`MediaRecorder`) or React Native modules (like `expo-av`, `react-native-audio`).

2.  **Pause & Chunk Detection**

    - Detect end of a phrase/sentence using:

      - Silence threshold (e.g., 700ms–2s of no speech).
      - Optional time limit (e.g., max 15s per chunk).

    - End current chunk, export audio blob, and begin transcription pipeline.

3.  **Transcription (STT)**

    - Send audio chunk to **Whisper** (via Replicate or hosted version).
    - Receive and return the transcribed text.

4.  **Streaming Option (if available)**

    - If using Gemini for both STT + chat, send audio stream directly and await text + response.

5.  **User Interrupt Detection**

    - If user begins speaking during playback, cut playback and resume recording.
    - Use real-time volume or mic signal to detect.

6.  **Local Buffering & Syncing**

    - Store each recorded chunk locally with:

      - Timestamp
      - Audio blob
      - Transcription

    - Send along with AI response to call log (for post-call feedback).

---

### ⚙️ Suggested Implementation Steps

- Hook into `MediaRecorder` or Expo AV’s audio API.
- Wrap audio chunks in `FormData` and send to `/transcribe` endpoint.
- If using Gemini for both STT + chat, build a wrapper for sending audio directly to Gemini endpoint and receiving structured response.
- Emit each transcribed chunk into the Supabase realtime stream or directly call the interaction endpoint.

---

### Bonus Ideas

- Add a visual indicator (waveform or dot) for “recording” and “waiting for AI”.
- Add ambient noise detection to improve silence accuracy.
- Optional: Add manual override (e.g., “tap to speak”) if auto-recording feels janky.
