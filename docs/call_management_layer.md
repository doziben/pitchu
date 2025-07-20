## ✅ CALL MANAGEMENT LAYER (Core Responsibilities)

This layer is responsible for:

1.  **Creating and tracking live calls**
2.  **Storing session metadata**
3.  **Assigning AI agents to users**
4.  **Ensuring clean termination and handoff to feedback layer**
5.  **Handling pricing logic (e.g. time used vs. time bought)**

---

## 🧩 CORE COMPONENTS & FEATURES

### 1. **Call Lifecycle**

Stage

Description

Call Start

A new row is created in `calls` table when the user initiates a session

Call Active

Real-time events (speech chunks, AI responses) flow in under this session

Call Ended

Triggered manually or via timer when time is up or user exits

---

### 2. **Data Model (Supabase Tables)**

sql

CopyEdit

`calls - id (uuid) - user_id - ai_agent_id (foreign key to agents table) - started_at (timestamp) - ended_at (timestamp) - duration_seconds - status (active, ended, error) - cost_estimate (numeric)`

sql

CopyEdit

`call_chunks - id (uuid) - call_id -  timestamp  - user_transcript (text) - ai_text_response (text) - ai_audio_url (text) - duration_seconds`

sql

CopyEdit

`agents - id (uuid) - name - model_type (e.g. Gemini, GPT-4) - tts_model - stt_model - description`

---

### 3. **State Management**

A simple store (on client) tracks:

- `currentCallId`
- `callStartedAt`
- `timeUsed`
- `callActive = true/false`
- `chunks[]` (pushed on every interaction round)

This helps:

- Resume calls if network blips
- Auto-end calls
- Show elapsed timer to user

---

### 4. **Pricing Logic**

You can precompute or estimate:

- Cost per second based on model config
- `$0.01 / min` for speech to text
- `$0.015 / min` for TTS
- `$0.002 / chunk` for chat response (assuming Gemini Flash)  
  Then multiply by time/chunk count.

Store this in `cost_estimate`.

Later, billing logic can use this table to deduct from purchased credits.

---

### 5. **Session Cleanup**

When the user ends call:

- `ended_at` is recorded
- Final transcript is compiled from chunks
- Chunk text is passed to **feedback layer**
- Audio blobs can be optionally cleaned up

---

### 6. **Security & Abuse Prevention**

- Limit 1 call per user at a time
- Optional call duration max (e.g. 15 min hard limit)
- Timeout after inactivity (e.g. no speech after 60s)

---

## 🛠 IMPLEMENTATION STEPS

1.  **Supabase**

    - Set up `calls`, `call_chunks`, and `agents` tables
    - Add RLS rules for `user_id`-scoped access

2.  **Client SDK**

    - Functions to `startCall()`, `endCall()`, `addChunk()`
    - Hook that tracks elapsed time, triggers end

3.  **Backend API/Socket**

    - Receives new audio chunks
    - Processes and responds (text + audio)
    - Appends to `call_chunks`

4.  **Call Timeout System**

    - Background job or serverless function
    - Ends idle calls after X seconds

5.  **Frontend UI**

    - Display ongoing call time
    - Handle retry/resume on reconnect
    - Trigger to end manually or on time expiry
