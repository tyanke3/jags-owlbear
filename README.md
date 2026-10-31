# JAGS Dice — an Owlbear Rodeo extension

Rolls the JAGS standard 4d6 with sixes counting as zero (0–20, centred on 10)
with 3D dice, keeps a shared roll log for the whole table, and lets each player
style their dice.
Press and hold a single die in the tray to roll just that one; it gets its own
log entry.

## Run it

You need Node.js 20 or newer.

```bash
npm install
npm run dev          # http://localhost:5173
```

Opening http://localhost:5173 in a normal browser tab runs it in **local
mode**: everything works except sharing, so you can tweak the UI fast.

### Try it inside Owlbear Rodeo

1. Keep `npm run dev` running.
2. In Owlbear Rodeo go to your Profile → **Add Extension** and paste
   `http://localhost:5173/manifest.json`.
3. Open a room, enable the extension for it, and click the dice icon in the
   top-left. Open the same room in a second browser (or a private window) to
   watch rolls appear for the "other player".

## Publish it

```bash
npm run build        # outputs to dist/
```

Upload `dist/` to any static host (Netlify, Vercel, Cloudflare Pages, GitHub
Pages). Players then add `https://your-site/manifest.json`. Bump `version` in
`public/manifest.json` when you ship changes.

## How it's put together

| File | What it does |
| --- | --- |
| `public/manifest.json` | Tells Owlbear the name, icon, popover size and background page. |
| `src/roll.ts` | The 4d6 roll with sixes as zero (crypto RNG) and the `RollEntry` type. |
| `src/dice3d.ts` | three.js scene: dice textures, materials, throw animation. |
| `src/log.ts` | Shared log in **room metadata** + **broadcast** for live rolls. |
| `src/style.ts` | Dice style, presets, saved to localStorage + **player metadata**. |
| `src/main.ts` | Popover UI wiring. |
| `src/background.ts` | Always-on page that shows a toast when someone else rolls. |

**Why the result is picked before the animation:** every player sees exactly
the same dice land, with no physics drift between clients. The animation
steers each die onto the chosen face.

**Log limits:** room metadata is small (about 16 KB shared by all
extensions), so the log keeps the last 40 rolls (`LOG_LIMIT` in
`src/constants.ts`).

## Ideas for next steps

- Roll against a target number and show success/failure margin.
- JAGS-specific results (e.g. flagging very high or low rolls).
- Sound on landing; a "roll privately to GM" option.
- More customisation: tray felt colour, dice size, number of dice.
- Change `ID` in `src/constants.ts` to your own domain before publishing.
