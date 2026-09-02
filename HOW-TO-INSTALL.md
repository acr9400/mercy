# Putting Mercy on your home screen

Free, no account, no subscription. Two pieces that do different jobs.

## Piece 1: the calendar file (do this first, takes 2 minutes)

`mercy-schedule.ics` holds your whole daily routine as recurring events with alerts,
plus her booster due dates, the rabies deadline, the vet appointment Friday, the free
exam expiry, and the date you can drop the walker.

1. Email the .ics file to yourself, or open it from Files on your phone.
2. Tap it. iOS offers to add the events. Choose "Add All".
3. Better: make a new calendar called Mercy first, and add them there, so you can hide
   or delete the whole set in one move later.

This is where your notifications actually come from. iPhone calendar alerts fire whether
or not any app is open, they survive a reboot, and they cost nothing. No web app can
match that for reliability.

## Piece 2: the app

**It is already live at https://acr9400.github.io/mercy/**

Open that in Safari on your iPhone, tap Share, then "Add to Home Screen", name it Mercy.
That is the whole thing. Everyone helping opens the same URL.

Owner key, for unlocking your own view when you sign in as Ariel: it is
`sync.owner_key` in `config.json`. Helpers never need it and never see your week.

To publish a change: edit `config.json`, run `python3 scripts/sync_app_config.py`, then
copy `app/` into the `mercy` repo working copy and push. GitHub Pages redeploys in about
a minute. iOS caches hard, so pull down to refresh, or delete the home screen shortcut
and add it back.

The public page carries no gym times and no sync token; `sync.publish_gym_defaults` is
false and the token is only emitted once `sync.url` is set. You set Your week inside the
app and it stays on your phone.

### How it was set up the first time

The app needs to be on the web to install to your home screen, hosted free on GitHub Pages.

1. Make a free account at github.com if you do not have one.
2. Create a new repository. Name it `mercy`. Set it to Public. Tick "Add a README".
3. On the repo page, click "Add file", then "Upload files".
4. Drag in all five files: index.html, manifest.json, sw.js, icon-192.png, icon-512.png.
   Click "Commit changes".
5. Go to Settings, then Pages in the left sidebar. Under Source pick "Deploy from a
   branch", branch `main`, folder `/ (root)`. Save.
6. Wait about a minute. The page will show your URL, something like
   `https://yourname.github.io/mercy/`.
7. Open that URL in Safari on your iPhone. Tap the Share button, then
   "Add to Home Screen". Name it Mercy.

It now runs full screen with no browser bars, works with no signal, and keeps your data
on the phone.

## Handing it to someone else

Send them the URL. Nothing else. On first open the app asks who they are, they type their
name once and pick a colour, and they are in. Their name syncs to your phone on the next
sync and shows up in your member bar. Everything they log is stamped with it.

### They see Mercy, not your life

Two views, decided by the name someone signs in with.

**You** get Your week, the gym button, and plan rows worded around your day: "out, in from
the gym", "out after learning", "out before you leave".

**Everyone else** gets the same times with neutral wording: "Out", "Afternoon visit". No
Your week section, no gym button, no idea where you are or what you are doing. The header
even changes from "Today's plan" to "Mercy's day".

Your week, your gym times, your role and your name preference live in your phone's storage
and are **never** sent to the shared log. Only dog events are ever posted. So even someone
who went looking at the Google Sheet would find outings, meals and accidents, and nothing
about your schedule.

Change `OWNER_KEY` near the top of the script block in `index.html` to something only you
know. If a helper types your name at sign-in, the app asks for that key before it will hand
over the owner view.

## Using a photo of Mercy as the icon

Put a photo in `photos/`, then from the repo root:

    python3 scripts/make_icons.py photos/mercy-headshot.jpeg

That generates every size the app needs, including the Android maskable versions inset on
the navy so adaptive shapes do not crop her face off, plus the small round avatar that
appears next to her name in the app header.

If the crop misses her face:

    python3 scripts/make_icons.py photos/mercy-headshot.jpeg --y 0.32 --zoom 1.4

Lower `--y` moves the crop up. `--zoom` above 1 crops tighter, and you want tighter than
feels right, because the icon renders about 12mm wide on a phone. A full body shot becomes
a brown smudge at that size. Her face filling the frame, good light, plain background.

Then re-upload the `app` folder to GitHub. **iOS caches home screen icons hard**: delete
the shortcut and add it back to see the new one. On Android a hard refresh is enough.

Until you run it, the app uses a plain paw mark on navy so nothing is broken.

## What the app does

- A live clock showing how long since she was last outside, measured against her actual
  bladder ceiling, which recalculates every day from her birthday. The bar fills as the
  time runs out and turns red when she is past it.
- Nothing is typed. Every input is a tap. Stool score is a row of 1 to 7 buttons with
  what each one looks like written under it.
- Live timers instead of guessing minutes. Tap Start walk when you head out, tap Done
  when you are back, and it logs the exact duration and both clock times. Same for play
  and for time she is left alone. The timer survives closing the app.
- A when selector for the times you forgot. Tap 30m ago, then log the event, and it
  back-dates it. Resets to Now after one use so you cannot leave it on by accident.
- Ceiling warnings on the spot: a walk over her five-minutes-per-month limit, a play
  session over 30 minutes, or time alone longer than her bladder hold all say so
  immediately.
- Today's plan as a real checklist. Tap a row and it logs that event at the actual clock
  time and ticks the box. Tap again to undo. Rows you blew past by more than half an hour
  turn red, so a missed 9:45 is visible instead of silently sliding by.
- Instant math on every tap. Log a poop and it tells you right there: "Poop logged 7:42 AM,
  22 min after her meal, 2h 10m since her last poop." You see the interval at the moment
  it happens, not at the end of the week.
- A "What she actually does" panel that learns her real numbers from your log: her median
  meal-to-poop window, her meal-to-pee window, her typical gap between pees, her most
  common poop hour, and the hour accidents cluster in. Each one stays greyed out until
  there is enough data to mean something, then turns gold.
- Running totals: outings against target, accidents, walk minutes, longest gap.
- Export CSV, which pastes straight into the Log tab of your workbook.

## Alerts that make noise

Tap **Alerts on** once, on each phone. After that, any running timer that crosses its
ceiling beeps and buzzes: a walk past her five-minutes-per-month limit, play past thirty
minutes, or time alone past her bladder hold. It repeats every ten minutes until you deal
with it. The banner turns red and shows how many minutes over you are.

The ceilings move on their own as she grows. Right now the walk ceiling is 11 minutes and
the alone ceiling is about 2h 13m. You do not maintain those numbers.

## Your week

The Your week section is a row per day. Tap a day to turn the gym on or off, set the time,
and it shows what time you would be back. Defaults are Sunday 7:00, Tuesday 5:00,
Thursday 5:00, Friday 6:00, at 75 minutes door to door.

Two things run off it. Today's plan shifts automatically: the first walk moves to twenty
minutes before you leave, and the second outing moves to your return time. And the
Going to the gym button checks whether she has been out in the last twenty minutes, makes
you take her out first if she has not, then runs the alone timer with your return time.

To get gym alerts in the background as well, set the same days and times in
`config.json` under `owner.gym` and rerun `python3 calendar/generate_ics.py`.

## Honest limits

The app can only notify you while it is open. That is a browser restriction, not
something I can code around, and it is why the calendar file exists. Use the calendar
for alerts and the app for logging and for the live clock.

Your data lives in this phone's browser storage. If you delete the app or clear Safari
data, it is gone. Export the CSV weekly into the workbook and treat that as the real
record.

## Changing it later

Her birthday is line 1 of the script block in index.html: `const DOB = new Date(2026,5,19);`
Months are zero-based, so 5 means June. The daily plan is the `PLAN` array a bit further
down. Edit, re-upload the file to GitHub, and the app updates itself next time you open it.
