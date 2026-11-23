OUTCOME
Netball association coaches run their whole 2027 rep season from one iPhone app: Expo (React Native, TypeScript), data on the phone, no login, $0 to build and run. Open it and see what's next; on the sideline, tally stats and court time one-handed without missing play; afterwards, everything is saved and easy to review. The design brief file in the repo (users, design principles, netball model, default stats, branding) is the spec for taste and detail; follow it. Features:
- Season calendar seeded from the 2027 rep calendar JSON (JSA, SSA, GBNL), filterable by program; coach edits events, times, venue, team.
- Compact "Next up" card on home: date, time, team, type, focus note.
- Day-before local reminders: who and when; reminder time adjustable.
- Session archive: photo of the paper drill sheet (camera or library) plus notes.
- Post-training reflection: went well, work on, 1-5 rating.
- Share a training plan photo via the iOS share sheet.
- Team roster; positions set per quarter, changes logged as timestamped events.
- Live stats: tap player, tap stat, +1, with undo; default positive and negative stats from the brief; coach adds custom stats; team and per-player totals by quarter.
- In-game per-player visual that shows at a glance who's struggling (e.g. too much stepping).
- Court time: player list, running timers, On/Off toggles, even-share indicator; correct after locking, backgrounding or killing the app.
- Game archive by date: stats, court times, notes for next training.

QUALITY BAR
Looks like the association's official tool, not a free AI-generated app: Wests maroon used with restraint, system font, real icons, no emoji, no gradients, consistent spacing. Built for a bright, noisy sideline: big thumb targets, two taps per stat, nothing ever lost.

CLOSE THE LOOP
Every iteration: typecheck, lint and unit tests pass (stat tally and undo, court-time maths across background/resume, day-before reminder scheduling, calendar seeding, next-session selection), and `npx expo export --platform ios` succeeds. Run the web build and drive it with Playwright at 390x844: build a roster, run a game across quarters with stats, subs and court time, reload mid-game and confirm nothing was lost, finish, reopen from the archive and check totals; check Next up for a faked "today". Screenshot every screen and critique it against the brief's principles, then fix what looks amateur. Native-only features (camera, notifications) get a web mock so flows still run.
Done = every feature works end to end in the Playwright run, all checks pass, screenshots meet the brief, and the README ends with a short "What you need to do" checklist for a non-technical coach.

AUTONOMY
Never stop to ask the coach anything. Make the sensible choice, note it in a decisions log in the repo, and keep going. Anything only a human can do (install Expo Go, scan the QR code, any paid account) goes in the README's final checklist in plain words, not in the middle of the run. The brief's Wests colours live in one theme file.

SCOPE
Not: home screen widget (next goal; keep next-session logic in a pure module a widget can reuse), email or SMS lists, accounts or cloud sync, Android polish, App Store submission, anything with a cost. Free to choose navigation, storage and UI libraries if they are free, open source, and work in Expo Go.

WHERE TO LOOK
The design brief and rep calendar JSON (no times in the source; leave them for the coach to fill in). Keep domain logic (stats, timers, schedule) apart from screens so it's testable.
