# Sky Theme UI Redesign Spec (Iteration 1)

## Iteration update: Start Screen landing refresh

### What changed
- Upgraded `StartScreen.razor` into a two-card landing layout:
  - **Hero card** with title, short value proposition, and primary CTA.
  - **How it works card** with 3 concise onboarding steps.
- Kept `OnStart` callback wiring unchanged (`@onclick="OnStart"`).
- Reused existing utility classes and sky tokens (no gameplay logic or flow changes).

### Design rationale
- **Entry-first clarity:** users now get a clear “why play” message before instructions.
- **Stronger action hierarchy:** primary CTA placed inside hero card for immediate start.
- **Digestible onboarding:** three numbered steps improve scanability on mobile.
- **Theme consistency:** white cards, sky shadow, and muted blue text preserve current Sky Theme.

### Notes
- No new CSS utilities were required for this iteration.

## Goal
Apply a light “Sky Theme” to existing screens **without changing component logic**, only class usage and utility tokens.

## Design direction
- Tone: bright, airy, calm (sky blue + cloud neutrals)
- Contrast: keep text readable on mobile outdoors
- Interaction: soft elevation + clear active/selected states

## 1) `SocOps/wwwroot/css/app.css` token plan

Add semantic tokens first, then map utility classes to them.

```css
:root {
  --sky-bg: #f3f9ff;
  --sky-surface: #ffffff;
  --sky-surface-soft: #eef6ff;
  --sky-border: #cfe3ff;

  --sky-primary: #2f80ed;
  --sky-primary-press: #1f6fd7;
  --sky-primary-soft: #dcebff;

  --sky-text-strong: #12304d;
  --sky-text: #2a4d6f;
  --sky-text-muted: #5f7d99;

  --sky-success-bg: #dff7ea;
  --sky-success-border: #6bcf97;
  --sky-success-text: #1e6b46;

  --sky-win-bg: #fff4cf;
  --sky-win-border: #f3c969;
  --sky-win-text: #7a5a10;

  --sky-overlay: rgba(9, 30, 66, 0.45);
}
```

### Utility-class mapping pattern
Keep existing class names and remap their values (minimal markup churn):
- `bg-gray-50` → `var(--sky-bg)`
- `bg-white` → `var(--sky-surface)`
- `border-gray-200`, `border-gray-300` → `var(--sky-border)`
- `bg-accent` → `var(--sky-primary)`
- `active:bg-accent-light` → `var(--sky-primary-press)`
- `text-gray-900` → `var(--sky-text-strong)`
- `text-gray-700`, `text-gray-800` → `var(--sky-text)`
- `text-gray-500`, `text-gray-600` → `var(--sky-text-muted)`
- `bg-marked` / `border-marked-border` / `text-green-800` → success token trio
- `bg-amber-200`, `border-amber-400`, `text-amber-900` → win token trio
- `bg-black/50` → `var(--sky-overlay)`

### Small utility additions (optional but useful)
```css
.bg-sky-gradient { background: linear-gradient(180deg, #f8fcff 0%, #edf6ff 100%); }
.shadow-sky { box-shadow: 0 8px 24px rgba(47, 128, 237, 0.12); }
.ring-sky { box-shadow: 0 0 0 3px rgba(47, 128, 237, 0.2); }
```

## 2) `SocOps/Components/StartScreen.razor`

### Class patterns
- Root container: keep structure, switch to sky backdrop
  - `... min-h-full p-6 bg-sky-gradient`
- Title/subtitle:
  - Title: `text-4xl font-bold text-gray-900 mb-2`
  - Subtitle: `text-lg text-gray-600 mb-8`
- How-to card:
  - `bg-white rounded-xl p-6 shadow-sky border border-gray-200 mb-8`
- Primary CTA:
  - `w-full bg-accent text-white font-semibold py-4 px-8 rounded-lg text-lg active:bg-accent-light transition-colors`

## 3) `SocOps/Components/GameScreen.razor`

### Class patterns
- Root: `flex flex-col min-h-full bg-sky-gradient`
- Header surface:
  - `flex items-center justify-between p-3 bg-white border-b border-gray-200`
- Back button:
  - `text-gray-500 text-sm px-3 py-1.5 rounded active:bg-gray-100`
- Instruction strip:
  - `text-center text-gray-500 text-sm py-2 px-4`
- Bingo indicator (win state):
  - `bg-amber-100 text-amber-800 text-center py-2 font-semibold text-sm`
- Board area: unchanged layout

## 4) `SocOps/Components/BingoSquare.razor`

Keep `GetCssClasses()` logic intact; only rely on remapped tokenized utilities.

### Existing state class outputs remain valid
- Base:
  - `... border border-gray-300 rounded ... text-xs ...`
- Marked + winning:
  - `bg-amber-200 border-amber-400 text-amber-900`
- Marked (non-winning):
  - `bg-marked border-marked-border text-green-800`
- Unmarked:
  - `bg-white text-gray-700 active:bg-gray-100`

### Optional polish (safe)
- Add `shadow-sm` to base for tactile tiles
- Add `ring-sky` when keyboard-focused (if focus utility introduced)

## 5) `SocOps/Components/BingoModal.razor`

### Class patterns
- Overlay:
  - `fixed inset-0 bg-black/50 flex items-center justify-center p-4 z-50`
- Modal card:
  - `bg-white rounded-xl p-6 max-w-xs w-full text-center shadow-xl animate-[bounce_0.5s_ease-out]`
  - optional: replace `shadow-xl` with `shadow-sky` for theme consistency
- Heading:
  - keep `text-amber-500` or shift to `text-gray-900` if less celebratory palette desired
- CTA:
  - same primary button pattern as Start screen

## 6) Optional `SocOps/wwwroot/index.html`

Set browser UI tint to Sky primary:

```html
<meta name="theme-color" content="#2f80ed" />
```

## 7) Rollout order (small/reversible)
1. Add CSS tokens + utility remaps in `app.css`.
2. Apply `bg-sky-gradient` + card shadow updates in `StartScreen.razor`.
3. Apply root/header polish in `GameScreen.razor`.
4. Visual verify bingo square states (no logic changes) in `BingoSquare.razor`.
5. Align modal surface/overlay in `BingoModal.razor`.
6. Optional `theme-color` update.

## Acceptance checklist
- No component logic changes (`@code` blocks untouched).
- All existing state classes still work.
- Readable contrast in default, marked, winning, and modal states.
- Mobile layout unchanged; visuals updated to Sky theme.

