# Quest — Daily Reading & Math Practice

An offline phone app: one reading passage (tuned to the reader's actual level, weighted
heaviest), ten math problems (matched to grade, 4th–8th), and a school-work check-in —
wrapped in an XP / coins / badges reward loop that's **tuned to what actually motivates
the kid using it**, not just a generic points system. No internet, no accounts, no cost.
Every answer is logged on-device so a parent can see real strengths and gaps.

This is an independent copy of the Quest codebase — it does not share progress, settings,
or deployment with any other copy. Edit this repo separately from here on.

---

## What this build includes

**Grades 4–8, both reading and math.** Onboarding asks the reader's grade directly and
sets a sensible starting Lexile from it (a parent can fine-tune both anytime in the
Parent tab). Math problems are grade-scoped to real Common Core content, not just
"easier numbers" — grade 4 gets multi-digit multiplication/division and fraction
equivalence, grade 5 adds unlike-denominator fractions and decimal operations, grade 6-8
covers integers through linear equations and the Pythagorean theorem.

**Rewards adapt to what motivates this specific kid, not just their answers.**
A short onboarding quiz sorts them into a type (Champion / Collector / Designer / Earner
/ Explorer / Star). Past that, the app also *watches what they actually do* — how fast
they open mystery boxes, whether they linger in the reward store, how often they revisit
badges — and can quietly shift which rewards get emphasized toward that real behavior.
This is **off by default** — a parent turns it on in **Parent → "Let real behavior
fine-tune rewards,"** where a plain-language line also shows what their behavior
suggests once there are ~5 days of data.

**Deeper, type-specific celebrations** on day-complete: a "New personal best" banner,
progress bars toward the next badge or reward, a surprise bonus mystery box for
Explorer-types, a real downloadable share card for Star-types, a confetti burst, and
optional soft chimes (off by default).

**Adaptive math, for free.** Problem selection quietly weights toward whatever topics
the real data shows this kid is actually missing — more practice on genuine weak spots,
less on what's already solid — using only the answers already being captured, no AI
service required.

**Built for iPhone specifically:** no rubber-band bounce, instant-registering taps, an
"Add to Home Screen" prompt, matching launch splash screens, and a built-in **read-aloud**
toggle (Safari's native voice, no cost).

**Two honest limitations:** iOS doesn't expose real haptic buzz to web apps (confetti +
chime substitute), and there's no push-notification reminder when the app is closed,
since this is a fully static site by design with no backend server.

---

## 1. Deploy it free on GitHub Pages (~5 minutes)

The whole site is the **`docs/`** folder. GitHub Pages serves that directly.

### Option A — drag-and-drop (no command line)
1. Go to https://github.com/new → pick a repo name (e.g. `quest-niece`, anything you
   like) → **Public** → Create.
   *(Public is required for free Pages. The repo holds only code + passages — no
   personal data. All of the reader's answers and progress live on their phone, never
   in the repo.)*
2. On the repo page: **Add file → Upload files**. Drag in **everything from this
   folder** (including the `docs/` folder, keep the structure) → Commit.
3. **Settings → Pages**. Under *Build and deployment*: Source = **Deploy from a
   branch**, Branch = **main**, Folder = **/docs** → Save.
4. Wait ~1 minute, refresh. The URL appears at the top:
   `https://<your-username>.github.io/<repo-name>/`

### Option B — command line
```bash
cd quest-niece
git init && git add . && git commit -m "Quest — initial deploy"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```
Then do step 3 above.

---

## 2. Put it on her iPhone home screen
1. Open the Pages URL **in Safari** (must be Safari for this to work on iOS).
2. Tap the **Share** icon → **Add to Home Screen** → **Add**.
3. It launches full-screen and works **offline** after the first open.

Use the **same phone** each time — progress and settings live on that device only.

---

## 3. First run
- The quiz sorts her into a motivation type, then onboarding asks her grade directly —
  both set sensible starting defaults, fully editable later in **Parent**.
- Add the **real-world reward** in Parent → Reward store, e.g. "🧸 Jellycat" — price it
  in coins using the existing ratio (the default $5-allowance item costs 150 coins, so
  ~30 coins per dollar is a reasonable starting point for pricing hers).
- In **Parent**, paste a grades-portal link if her school uses one, so the school-work
  mission can deep-link to it.

---

## 4. The data this captures
**Parent tab → "Model-B data."** Every session logs, per answer: each reading
question's type and whether it was right, her open-response writing, math topic-level
right/wrong with timing, school-work check-ins, and reward redemptions. Export as
**.json** or copy to clipboard anytime. The **Insights** panel above it already
surfaces her weakest math topics and reading-question types from real answers.

---

## 5. Adding more passages
The bank is **`src/passages.js`** — a plain array, currently 26 passages spanning
roughly **640L–1080L** (grade 4 through grade 8). Add new objects in the same shape
(`title, genre, lexile, passage, mc[5], open`), then rebuild:

```bash
npx esbuild src/main.jsx --bundle --minify --outfile=docs/app.js --loader:.js=jsx
cp public/* docs/
```
Commit and push — Pages redeploys automatically. Bump the cache name in `public/sw.js`
(currently `quest-v5`) whenever you rebuild, so the phone picks up the new version
instead of serving a stale cached one.

The 1075L "Stretch" tier is thin (2 passages) — worth adding a few more there first if
this profile is set to an advanced grade 7-8 reader.

---

## Structure
```
src/        app.jsx (app), passages.js (reading bank), styles.css, main.jsx (entry)
public/     index.html, manifest.json, sw.js, icons, splash screens, .nojekyll
docs/       ← the built, deployable site (this is what Pages serves)
```
Built with React 19 + esbuild. No backend, no API keys, no tracking, no ads.
