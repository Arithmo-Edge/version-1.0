# CLAUDE.md — Arithmo-Edge (BdMO Warm-Up & Modular Core v1.0)

## 1. Project Vision & Architectural Philosophy
We are building **Arithmo-Edge** ("The World's First Real-Time ELO-Rated Competitive Mathematics & Olympiad Mega-Ecosystem", powered by Olympiad Edge). 
- **Core Rule (Zero-Throwaway Architecture):** Every line of code written now for the **BdMO Special Warm-Up Championship** must serve as the permanent foundation for our future 14-portal federated ecosystem, 5 Arenas (60-Tier ELO), and 42-Engine AI Core. Do NOT write throwaway spaghetti code. Build modular, plug-and-play components.
- **Zero-Burn Stack (\$0/Month):** Static HTML/CSS/Vanilla JS (ES6 Modules) hosted on GitHub Pages, Firebase (Auth + Firestore), Cloudflare Workers (API routing & security), and Cloudflare R2 (handwritten solution image storage).

---

## 2. Multi-Repository Separation (Strict Security Rule)
To prevent problem leaks via Git commit history, **NO question JSON files shall ever exist in this Core Repository.**
- **Repo 1 (`arithmo-edge-core` - This Repo):** Contains `sdk/engine.js`, UI portals (`/contest`, `/standings`), and empty placeholder hooks for future subsites.
- **Repo 2 (`arithmo-bdmo-vault` - Separate Repo):** Hosts the 4 category JSON files (`primary.json`, `junior.json`, `secondary.json`, `higher-secondary.json`) on a separate GitHub Pages / Worker endpoint.
- `engine.js` must fetch problems dynamically using a configurable `CONFIG.VAULT_BASE_URL` constant, enforcing a strict client-side + server-time countdown lock (`T-0`) before rendering any problem text.

---

## 3. Directory Structure (14-Portal Ready)
Maintain this exact folder structure so subdirectories can later be mapped directly to custom subdomains (`://arithmoedge.com`, `://arithmoedge.com`):

```text
/
├── CLAUDE.md                 # This master specification
├── index.html                # Landing & Category Hub (Primary, Junior, Secondary, Higher Secondary)
├── /sdk/
│   └── engine.js             # Arithmo-Core-SDK (Auth, Read-Shield Cache, R2 Upload, Future Hooks)
├── /contest/
│   └── index.html            # Live BdMO Warm-Up Arena (KaTeX math rendering, Timer, Submission)
├── /standings/
│   └── index.html            # Real-Time & Post-Match Leaderboard (Rated + Unrated filtering)
└── /assets/
    └── style.css             # Unified Cyber-Olympiad Dark/Light UI Theme
```

---

## 4. Identity, Firestore Schema & Rated vs. Unrated Logic
1. **Unrated Guest Mode (Frictionless Entry):**
   - Users without an account can enter a Display Name and compete immediately.
   - Store a temporary `guest_<uuid>` in `localStorage`.
   - Guests appear on `/standings/` with a clear `[Unrated]` badge and **0 ELO change**. Do not write guest sessions to Firestore unless they submit a solution (tagged `is_rated: false`).
2. **Rated Mode (`@handle` via Gmail SSO):**
   - Users sign in via Firebase Google Auth and claim a unique lowercase `@handle` (`users/{uid}`).
   - Initialize the **Full Future-Proof Firestore Document** on signup so future versions never require database migration:
   ```json
   {
     "uid": "firebase_uid",
     "handle": "@rayaan",
     "display_name": "Rayaan Tasnim",
     "is_rated": true,
     "elo": {
       "mixed": 1200,
       "number_theory": 1200,
       "algebra": 1200,
       "geometry": 1200,
       "combinatorics": 1200
     },
     "subscription": "Free",
     "wallet_balance": 0.00,
     "hq_elo": 0,
     "created_at": "SERVER_TIMESTAMP"
   }
   ```
   *(Note: BdMO Warm-Up updates the `elo.mixed` rating upon T+3h finalization).*

---

## 5. Contest Engine & Dual-Mode Submission Pipeline
Each problem in the external Vault JSON specifies `"type": "VALUE"` or `"type": "PROOF"`. Max **2 submissions per problem**.
1. **Client-Side Image Compression (Mandatory):**
   - Before uploading any handwritten solution photo, compress it in-browser using HTML5 Canvas to **WebP format (~150KB–250KB, max width 1600px)** so handwriting and geometry diagrams remain crisp while keeping storage costs at $0.
   - Upload directly via presigned URL / Cloudflare Worker to **Cloudflare R2**.
2. **Submission UI by Problem Type:**
   - **If `type === "VALUE"`:** Require BOTH the handwritten work photo AND a text input box for the final numerical/algebraic value (units optional; backend SymPy handles unit-agnostic normalization).
   - **If `type === "PROOF"`:** Require the complete handwritten proof photo AND a prominent confirmation button **`[ ✓ প্রমাণিত (Proved) ]`** (sets `user_text_input: "PROVED"`).

---

## 6. User Trust & Assurance UX ("আশ্বাস" Design Principles)
To build immediate trust and confidence among Olympiad students:
- **Live Telemetry & Security Badges:** Display subtle, authoritative status indicators in the header: `● QPRT-4 Read-Shield Active`, `● 256-Bit Submission Lock`, and clear `RATED (@handle)` vs `UNRATED (Guest)` status pills.
- **Instant Submission Assurance:** Immediately upon upload, show a clear green receipt card with a unique **Submission Hash ID**, timestamp, image preview thumbnail, and status: *"Handwritten Script Received & Queued for SymPy + AI Rubric Audit"*.
- **Transparent Rating Notice:** Clearly display on the leaderboard: *"Provisional Standings • Official Rated ELO (`mixed`) updates at T+3h post-audit."*
- **Resilience:** Auto-save unsent text inputs to `localStorage` so an accidental page refresh or brief ISP drop never loses user progress.

---

## 7. Future Plug-and-Play Hooks in `sdk/engine.js`
Include empty, documented placeholder methods in `engine.js` for upcoming releases (do not implement yet, just keep clean interfaces):
- `export async function aiMentorHook(payload) { /* Reserved for V3.0 42-Engine AI Core */ }`
- `export async function walletCheckoutHook(item) { /* Reserved for V3.2 Arithmo Wallet */ }`
- `export async function module7ProctorHook(session) { /* Reserved for V2.0/Sunday Mega Gaze Guard */ }`

---

## 8. Strict Guardrails for Claude
1. **Never expose API Keys:** No Gemini, R2 Secret, or Admin keys in frontend JS. Only public Firebase config is allowed.
2. **Read-Shield Caching:** Never query Firestore in a loop for leaderboard updates. `/standings/` must read from a single cached JSON snapshot endpoint updated periodically.
3. **Incremental Edits:** When asked to build V1.1 or V2.0 features later, ONLY modify the target module or hook. Never rewrite `sdk/engine.js` auth/schema logic from scratch.
