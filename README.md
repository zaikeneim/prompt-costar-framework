# CO-STAR — Prompt Engineering Framework

A single-file, zero-dependency React application for composing high-precision AI prompts using the **CO-STAR** framework. Fill the six structured sections, preview the assembled prompt, and optionally send it directly to Claude via the Anthropic API — all from one HTML file.

---

## What is CO-STAR?

CO-STAR is a prompt engineering framework introduced by [Sheila Teo](https://towardsdatascience.com/how-i-won-singapores-gpt-4-prompt-engineering-competition-34c195a93d41) and documented in the [Prompt Engineering Cheat Sheet](https://medium.com/the-generator/the-perfect-prompt-prompt-engineering-cheat-sheet-d0b9c62a2bba) by Maximilian Vogel. It breaks a prompt into six focused components, each addressing a distinct dimension of the LLM's task:

| Letter | Component | Purpose |
|--------|-----------|---------|
| **C** | **Context** | Background details that frame the situation for the model |
| **O** | **Objective** | The single, precise task you want accomplished |
| **S** | **Style** | Writing conventions, structure, and voice |
| **T** | **Tone** | Emotional register and attitude of the response |
| **A** | **Audience** | The target reader — role, expertise level, assumed knowledge |
| **R** | **Response** | Output format, length, structure, and hard constraints |

By filling each section explicitly, you eliminate the ambiguity that causes LLMs to produce generic, misdirected, or incomplete responses.

---

## Getting Started

No build step, no package manager, no local server required.

```bash
# 1. Download the file
curl -O https://your-host/costar-framework.html

# 2. Open in browser
open costar-framework.html          # macOS
start costar-framework.html         # Windows
xdg-open costar-framework.html      # Linux
```

The application runs entirely in the browser. The only network requests are:

- Google Fonts (loaded from `fonts.googleapis.com`) — display only, not required for functionality
- Anthropic API (`api.anthropic.com`) — only when API mode is enabled and a key is provided

---

## Features

### Prompt Composition
- Six color-coded input sections, one per CO-STAR component
- Context-aware placeholders with concrete Java/enterprise examples
- Live character counter per section
- Border glow and color feedback on focus and completion

### Progress Tracking
- Header pip indicators — one per section, colored when filled
- Section count displayed in the assembled prompt panel

### Assembled Prompt Panel
- Auto-assembles filled sections into a structured Markdown-formatted prompt
- Sections separated by `---` dividers with labeled headers (`## C — Context`, etc.)
- One-click copy to clipboard with visual confirmation
- Clear All resets all fields and the response area

### API Mode (toggle in header)
- Enter your Anthropic API key (stored in memory only, never persisted)
- Fixed model: `claude-sonnet-4-20250514`
- Sends the assembled prompt to the Anthropic Messages API
- Response rendered as full Markdown: headings, code blocks, tables, blockquotes
- API errors surfaced with the raw message from the API response

---

## API Key Security

The API key is held in React component state for the duration of the page session. It is:

- **Never written to `localStorage`, `sessionStorage`, or cookies**
- **Never sent to any server other than `api.anthropic.com`**
- **Cleared on page reload**

The direct browser-to-API call requires the header `anthropic-dangerous-direct-browser-access: true`, which is included in the request. Anthropic permits this for local development and tooling use cases. Do not deploy this application to a public host with a hardcoded API key.

---

## Dependencies

All loaded from CDN at runtime. No `package.json`, no `node_modules`.

| Library | Version | Source |
|---------|---------|--------|
| React | 18 (production) | `unpkg.com` |
| ReactDOM | 18 (production) | `unpkg.com` |
| Babel Standalone | latest | `unpkg.com` |
| marked | latest | `cdn.jsdelivr.net` |

### Fonts (Google Fonts)

| Font | Usage |
|------|-------|
| Syne 700/800 | Display headings, section labels, logo |
| JetBrains Mono 400/500 | Prompt output, code, metadata |
| Outfit 300/400/500/600 | Body text, descriptions, UI |

---

## Technical Notes

### No Build Step

The application uses Babel Standalone to transpile JSX in the browser at load time. This is intentional — the goal is a portable, single-file tool. For a production deployment with significant traffic, replace Babel Standalone with a pre-compiled bundle.

### CORS

Direct browser calls to `api.anthropic.com` require the `anthropic-dangerous-direct-browser-access: true` header. This is supported by Anthropic for local tooling. If you deploy behind a reverse proxy or want to avoid exposing the API key in the browser, replace the `fetch` call in `handleGenerate` with a call to your own backend endpoint.

### Extending CO-STAR

The `SECTIONS` array in the script block is the single source of truth for all six components. To add, remove, or reorder components, edit that array. Each entry takes:

```js
{
  key:    string,   // field name in state
  letter: string,   // single-character label
  label:  string,   // full name
  color:  string,   // CSS hex color
  rgb:    string,   // "r,g,b" for rgba() usage
  desc:   string,   // description shown in the card
  hint:   string,   // short hint chip text
  ph:     string,   // textarea placeholder
}
```

---

## References

- Sheila Teo — [How I Won Singapore's GPT-4 Prompt Engineering Competition](https://towardsdatascience.com/how-i-won-singapores-gpt-4-prompt-engineering-competition-34c195a93d41)
- Maximilian Vogel — [The Perfect Prompt: A Prompt Engineering Cheat Sheet](https://medium.com/the-generator/the-perfect-prompt-prompt-engineering-cheat-sheet-d0b9c62a2bba)
- Anthropic — [Messages API Reference](https://docs.anthropic.com/en/api/messages)
