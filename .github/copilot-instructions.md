# Repo-specific Copilot Instructions

This repository is a small, client-side Quizlet-like single-page app. The goal of this file is to give AI coding agents concise, actionable guidance so they can be productive immediately.

**Big Picture:**
- **Type:** Single-page web app (no backend). The majority of app logic is embedded in `index.html`.
- **Static assets:** `public/` holds served static resources (e.g. `public/soundeffects/`). Vite will serve `public/` at root in development.
- **Source files:** `src/` contains styles and a couple small JS files (`src/main.js`, `src/counter.js`), but much of the app state and UI logic lives inline in `index.html`.

**Developer workflows / commands**
- **Install deps:** `npm install`
- **Dev server:** `npm run dev` (uses Vite)
- **Build:** `npm run build`
- **Preview production build:** `npm run preview`

**Where to make changes**
- If you need to change UX or game logic, start in `index.html` near the big inline `<script>` (top-to-bottom script defines global state and functions like `processInput`, `initFlashcards`, `resetQuizState`, `loadNextQuestion`, `generateQuizOptions`, `handleAnswer`, and typing-quiz equivalents).
- Small helper modules to inspect: `src/main.js` and `src/counter.js` — check them if you prefer extracting behavior from the inline script.
- CSS: `src/app-style.css` and `style.css` control layout and theme.

**Important patterns & conventions found in this codebase**
- Global state: many top-level `let` variables (e.g., `vocabList`, `quizQueue`, `masteredTerms`) are used across functions. Do not convert pieces to local scope without updating all usages.
- DOM-by-id: code frequently uses `document.getElementById('...')` and inline `onclick="..."` attributes. Keep `id` attributes intact when refactoring.
- Single-file logic: index.html contains event wiring, UI, and logic together — extracting functions should preserve the same global names or update the HTML attributes.
- Audio loading: audio is created with `new Audio('/soundeffects/correct.mp3')` and has `onerror` fallbacks to `./public/soundeffects/...`. When adding audio, put files in `public/soundeffects/` so Vite serves them at `/soundeffects/...` during dev.

**Key files & snippets to reference**
- `index.html` — primary app logic and UI. Examples:
  - `processInput()` — parses pasted text into `vocabList`.
  - `exportFile()` / `importFile()` — data export/import using plain text blobs.
  - `generateQuizOptions()` / `handleAnswer()` — multiple-choice quiz logic with re-queueing for reinforcement.
  - `submitTypingAnswer()` / `loadNextTypingQuestion()` — typing-quiz flow.
- `public/soundeffects/` — add audio assets here; code expects `/soundeffects/<name>.mp3`.

**Integration & behavior notes**
- Vite development: `public/` becomes root; absolute paths beginning with `/` refer to `public/` content. Keep this in mind when changing asset URLs.
- Persistence: there is no automatic persistence; the app keeps data in-memory only. `exportFile()` is the suggested way to persist user-created sets.
- Minimum items: several features check minimum vocab size (e.g., multiple-choice quiz requires >= 4 terms). Preserve these checks or update UI messaging when changing logic.

**Suggested AI agent behavior**
- Prefer minimal, single-purpose edits. Because the app mixes DOM + logic in `index.html`, large refactors should be proposed first as PRs rather than applied blindly.
- When editing logic that affects UI element IDs or inline `onclick` attributes, update both the DOM and the functions referencing them.
- If adding tests or a new build step, document new commands in `package.json` scripts.

If any area is unclear (for example, you want me to extract quiz logic into `src/quiz.js` or add unit tests), tell me which part to change and I will prepare a focused patch and update these instructions accordingly.
