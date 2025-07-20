## 📝 Transcript Layer

**Goal:**  
Continuously capture and structure the full conversation (both user and AI sides) for use in real-time display, playback, feedback, and future export/sharing.

---

### 🔩 Core Responsibilities

1.  **Capture and Store Chunks in Real-Time**

    - As each **user speech chunk** is processed:

      - Store transcribed text (from STT)
      - Store timestamp

    - As each **AI response** is generated:

      - Store both **text** and **audio_url** (from TTS)
      - Link to corresponding user chunk (optional)

    - Table: `call_transcripts`

      ts

      CopyEdit

      `{ id: uuid, call_id: uuid, role: 'user' | 'ai', text: string, audio_url: string | null, start_time: timestamp, end_time: timestamp
}`

2.  **Provide Live Transcript View**

    - Frontend subscribes to call_id via Supabase Realtime
    - Display role-based dialog stream:

      vbnet

      CopyEdit

      `User: Hey, I’m trying to increase conversion… AI: Can you tell me more about your target audience?
...`

3.  **Rebuild Full Conversation**

    - Query all `call_transcripts` by `call_id` and order by `start_time`
    - Used by:

      - Feedback Layer
      - Export/Share
      - Playback/Review
      - Billing

4.  **Handle Edge Cases**

    - Sometimes a chunk might fail (e.g., AI didn’t respond). Track that state and possibly retry.
    - Track `duration` for each chunk as backup to audio metadata

---

### 💡 Optional Features

- **Export Transcript** to:

  - PDF / Markdown / Shareable Web View

- **Playback Controls**:

  - Reconstruct full audio from chunked AI audio
  - Let user click to "re-listen" to any part

- **Search** inside transcript
- **Timestamped bookmarks / highlights** (for coaching)

---

### ⚙️ Implementation Notes

- Store raw text even if audio is lost (e.g., playback failed)
- Consider adding `confidence_score` from STT for debugging or quality grading
- For storage efficiency: only store audio URLs if playback is needed; otherwise just keep text
