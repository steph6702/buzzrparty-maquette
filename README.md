# Buzzrparty — standalone prototype

A single-file, self-contained demo of the Buzzrparty buzzer game. No build step,
no framework, no package install. Open the HTML file in a browser and it runs.

**Live:** https://steph6702.github.io/buzzrparty-maquette/proto/

---

## The game

A clip plays. It **freezes dead on the action**. You must call what is about to
happen — one of two options — *before* you see it. Then the clip runs on and
shows whether you were right.

Every correct call scores `basePoints`. Committing **before** the freeze earns
up to `earlyBonus` more, scaled across `earlyWindow` seconds. Calling it at the
freeze earns the base only. So anticipation beats reaction, which is the point.

---

## Files

```
proto/index.html     the whole app — markup, styles, logic
index.html           a separate static page of proposed fixes to the live site
```

That's it. There is nothing else to install or run.

---

## Changing things

### The playlist

The app reads a real Buzzrparty playlist from the Playlist Studio API at load,
and falls back to five hardcoded clips if the API can't be reached.

Play a different playlist with **no code change** — just add it to the URL:

```
.../proto/?playlist=8mqXvk3zcgtFdf0mW0Lk
```

To change the default, edit `CONFIG.playlistId` near the top of the `<script>`.

Each clip needs four things from the playlist data, and clips missing any of
them are skipped automatically:

| Needs | Comes from |
|---|---|
| video id | the YouTube embed URL in `video.iframe` |
| trim in / out | `startvid` / `endvid` |
| the freeze point | `video.timeaction` |
| the two options | `raw.namebuzzerLeft` / `namebuzzerRight` (FR), `raw.namebuzzLeftA` / `namebuzzRightA` (EN) |
| which is right | `video.correct_buzzer` |

### Branding

All colours live in the `BRAND` block at the top of the `<style>`. Change them
there and the whole app follows — every screen reads from those names.

There are two palettes: the normal one, and **Match Mode**, the dark state the
app flips into while a clip plays. Match Mode is defined under
`.device[data-mode="match"]`.

The wordmark is the `.wordmark` div in the top bar. Swap the text or drop an
`<img>` / inline `<svg>` in its place.

### Game tuning

Everything numeric is in the `CONFIG` object at the top of the `<script>`:
rounds per game, lead-in time, how long the freeze holds, the scoring numbers,
the final-round multiplier, and the simulated opponents and their skill.

---

## Known limits

- **Opponents are simulated.** There is no multiplayer and no backend. Real
  multiplayer needs a server; everything else here does not.
- **YouTube ads cannot be removed.** They can only be moved out of the way.
  Each clip is loaded a round early so its pre-roll runs during the previous
  verdict, and the countdown will not start until the real footage is on
  screen. Round one still tends to eat one. Stripping ads from an embed breaks
  YouTube's terms — don't.
- **Timing accuracy** comes from the YouTube player's own clock, read at the
  moment of the tap. It is good to well under a tenth of a second, which is
  far inside the scoring window. It is not frame-exact.
- **The QR code is decorative.** It encodes nothing.
- **No persistence.** Refreshing loses scores.

## Data notes

Two things spotted in the live `ICONIC MOMENTS` playlist that are worth fixing
at the source rather than working around here:

- Two clips (Zlatan, Drogba) have placeholder option labels — literally "Left"
  and "Right" instead of descriptions of the action.
- `correct_buzzer` is stored as `"Left"`/`"Right"` on most clips but `"Gauche"`
  on at least one. The parser accepts both, but anything else reading this data
  probably won't.
