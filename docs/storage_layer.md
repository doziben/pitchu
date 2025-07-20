## 🧰 Storage Layer

**Goal:**  
Store and retrieve all necessary assets generated or used during a call, including AI-generated audio, user-uploaded recordings (optional), transcripts (if large), and session metadata.

---

### 🔩 Core Responsibilities

1.  **Audio File Storage**

    - Save **AI-generated audio responses** (from OpenAI TTS) to storage.
    - URL stored in `call_transcripts.audio_url`.
    - Naming convention:

      bash

      CopyEdit

      `calls/{call_id}/ai_chunks/{chunk_id}.mp3`

    - Stored immediately after TTS finishes.

2.  **User Audio (Optional)**

    - Optionally store **raw user audio chunks** if needed for analysis or playback (e.g., for coaching).
    - Could be gated behind a permission/consent toggle.
    - Naming convention:

      bash

      CopyEdit

      `calls/{call_id}/user_chunks/{chunk_id}.webm`

3.  **Full Call Archive**

    - Optionally store a bundled JSON of:

      - Full transcript
      - All audio URLs
      - Feedback summary

    - Used for export, playback, or coaching archive.
    - Path:

      bash

      CopyEdit

      `calls/{call_id}/archive.json`

4.  **Storage Backend Options**

    - **Supabase Storage**:

      - Easiest integration
      - Realtime access control via Supabase RLS

    - **Cloudflare R2**:

      - More cost-efficient for scale
      - Compatible with S3 API
      - Ideal for long-term storage

5.  **Access Control**

    - Files are private by default.
    - Use signed URLs for playback access (e.g., expiring in 15 min).
    - Only allow users to access their own call files.

---

### 🛡️ Security & Permissions

- RLS in Supabase: `calls.user_id = auth.uid()`
- Only save audio if call duration is within plan limit (tie in Billing Layer)
- Auto-delete audio after X days on free plan (if needed)

---

### 💡 Optional Features

- **Data retention policy**

  - E.g. keep free-tier call assets for 7 days

- **Cleanup job**

  - Cron to remove expired or incomplete calls

- **Download/export**

  - Let user download a `.zip` of transcript + audio
