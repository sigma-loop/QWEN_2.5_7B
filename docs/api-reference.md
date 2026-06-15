
# API Reference: Codeforces Tutor AI Engine

This document outlines the API endpoint available for interacting with the fine-tuned Qwen2.5-Coder-7B model server. The server is exposed publicly via Ngrok on port 5000.

## Base URL
`http://<your-ngrok-public-url>.ngrok-free.app`

---

## Codeforces Generation Endpoint

### `POST /generate`
An omnibus endpoint that dynamically routes traffic based on the JSON payload structure. It supports isolated single-problem evaluation (for hints or full code generation) and multi-turn interactive chat sessions.

### Headers
| Header | Value | Description |
| :--- | :--- | :--- |
| `Content-Type` | `application/json` | Required |

---

### Request Body Options

The backend handles incoming requests through two distinct modes determined by the presence of either the `messages` array or the `problem` string block.

#### Option A: Chat Mode (Multi-Turn Conversation)
Use this option when integrating with a continuous chat sidebar UI component. The backend automatically injects a standard conversational system routing instruction before appending your chat state history.

| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `messages` | `array` | Yes | An array of message objects containing alternating user requests and assistant tracking logs. |

**Example Chat Payload:**
```json
{
  "messages": [
    {"role": "user", "content": "I am stuck on understanding recursion."},
    {"role": "assistant", "content": "Recursion occurs when a function calls itself to solve smaller instances of the same problem. What part is causing confusion?"},
    {"role": "user", "content": "How do I ensure the function actually stops?"}
  ]
}

```

#### Option B: Structured Problem Mode (Hints / Full Solutions)

Use this mode when triggering standalone actions directly from problem pages via targeted UI buttons (such as "Request Hint" or "View Solution").

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `problem` | `string` | Yes | The raw descriptive text or snippet parsed directly from the competitive programming site interface. |
| `rating` | `string/int` | No | The official platform baseline difficulty ranking. Defaults internally to `"Unknown"`. |
| `topics` | `string` | No | Extracted algorithm classification tags (e.g., `"graphs, dp"`). Defaults internally to `"Unknown"`. |
| `mode` | `string` | No | Valid switches: `"hint"` (enforces a highly concise 1-2 sentence theoretical insight without code snippets) or `"solution"` (generates an implementation script). Defaults to `"hint"`. |

**Example Hint Payload:**

```json
{
  "problem": "Given an integer w, determine if a watermelon can be split into two even-weighted pieces...",
  "rating": "800",
  "topics": "math, brute force",
  "mode": "hint"
}

```

**Example Solution Payload:**

```json
{
  "problem": "Given an integer w, determine if a watermelon can be split into two even-weighted pieces...",
  "rating": "800",
  "topics": "math, brute force",
  "mode": "solution"
}

```

---

### Response (JSON)

#### Success (`200 OK`)

Returns a standard JSON array packaging a single element wrapper enclosing the text generation output payload string.

```json
[
  {
    "generated_text": "Verify if the primary structural parameter value exceeds two while maintaining a zero remainder under a modulo operation by two."
  }
]

```

#### Error Handling (`500 Internal Server Error`)

Returned if runtime failures or execution resource starvation faults break the processing path.

```json
{
  "error": "CUDA out of memory error. Allocation failed..."
}

```

```

```
