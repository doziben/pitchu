## ✅ Feedback Layer

**Goal:**  
Generate meaningful, constructive feedback after each call by analyzing the full conversation (user speech + AI responses), helping users improve over time.

---

### 🔩 Core Responsibilities

1.  **Wait for Call Completion**

    - Triggered once a call ends (`call.status = 'ended'`).
    - Could be a Supabase Edge Function, cron job, or backend listener.

2.  **Fetch Full Conversation**

    - Query all chunks for the given `call_id`:

      - User transcript
      - AI response

    - Format as structured turn-based dialogue for analysis:

      json

      CopyEdit

      `[  { role:  "user", text:  "Hi, I'm interested in your product..."  },  { role:  "ai", text:  "Great! Can I ask what problem you're trying to solve?"  }, ... ]`

3.  **Send to Feedback Model**

    - Use Claude Haiku or GPT-4o (Claude is cheaper/better for longer text).
    - Prompt:

      pgsql

      CopyEdit

      `Here's a mock sales call transcript. Evaluate the user's performance as a sales rep.

      - Point out 2 things they did well
      - Point out 2 areas they could improve
      - Suggest 1 actionable tip
      - Keep it brief and conversational`

    - Optionally include custom criteria (objection handling, clarity, pacing, etc.)

4.  **Return Structured Feedback**  
    Example:

    json

    CopyEdit

    `{  "positive":  ["You kept a friendly tone throughout.",  "You asked good discovery questions."],  "improvements":  ["You missed a buying signal at minute 4.",  "Too much filler language at times."],  "tip":  "Next time, try summarizing their need before pitching."  }`

5.  **Save to Supabase**

    - Table: `call_feedback`

      - `call_id`
      - `feedback_json`
      - `created_at`

---

### 💡 Optional Features

- **User upvotes/downvotes feedback** to improve system
- Generate **"score"** (e.g., 1–10) for gamified experience
- Future: **Team Leaderboards** based on average score
- Tag transcript moments with strengths/weaknesses

---

### ⚙️ Implementation Notes

- Don’t process during call — only post-call
- Can be queued or triggered via a lightweight job
- Consider timeouts or cost limits (e.g., skip if transcript is empty)
