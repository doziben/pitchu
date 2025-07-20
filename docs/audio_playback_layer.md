### 🎧 Audio Playback Layer

**Goal:**  
Ensure that the AI’s responses are played smoothly and with minimal delay, while handling edge cases like user interruptions or back-to-back chunked responses.

---

### 🔩 Core Responsibilities

1.  **Audio Queue Management**

    - Maintain a local queue of audio chunks returned from the interaction layer.
    - Ensure chunks are played in sequence and prevent overlapping audio.

2.  **Streaming TTS Handling**

    - Support streamed audio (if the TTS model supports it).
    - Begin playback even before the full response is synthesized.

3.  **Interrupt Handling**

    - If the user starts speaking again before playback ends, auto-stop current playback.
    - Optionally, discard or store the interrupted chunk for potential replay.

4.  **Playback Progress Tracking**

    - Monitor and log start/end timestamps of each audio playback chunk.
    - Useful for syncing with the transcript and analytics later.

5.  **UI Feedback**

    - Display a waveform, playhead, or voice indicator while audio is playing.
    - Provide visual cue for "AI is speaking" vs "User is speaking".

---

### ⚙️ Suggested Implementation Steps

- Use a `playbackManager` object/module with:

  - `enqueue(audioBlob)`: Add to queue.
  - `playNext()`: Play the next blob in queue.
  - `stop()`: Force-stop current playback.
  - `isPlaying()`: Status checker.

- Leverage native HTML5 Audio or Expo AV in React Native (if mobile).
- Track timestamps in local state or Supabase if needed server-side.
