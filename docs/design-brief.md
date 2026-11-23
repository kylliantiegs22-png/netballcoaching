# Netball Coaching App: Design Brief

Research summary to guide the build. Put this file in the repo next to the calendar data (for example `docs/design-brief.md`).

## Who uses it
Netball association coaches, in this case Western Districts Netball Association (Wests), Faulkner Park, Graceville, Brisbane. They coach rep teams (JSA, SSA, GBNL) and junior club teams. Most are volunteers or parents, not technical. They use the app outdoors on a sideline: in bright sun or under night-court lights, phone in one hand, clipboard or bib bag in the other, and their eyes mostly on the court.

## Design principles
1. **Glance, tap, look back up.** Every in-game action takes at most two taps. No typing during a game. No confirmation dialogs mid-game; use undo instead.
2. **Thumb zone.** Primary game controls sit in the bottom two thirds of the screen. Touch targets are at least 44pt, and in-game buttons at least 56pt.
3. **Readable in sunlight.** High contrast (WCAG AA minimum, AAA for in-game numbers), large numerals, and no meaning carried by colour alone. Pair colour with an icon or a sign: a tick or + for positive stats, a cross or − for negative ones.
4. **Never lose data.** Write every tap to storage immediately. Timers are stored as start timestamps, not counters, so they stay correct after the phone locks, the app is backgrounded or killed, or a call comes in. Reopening a live game resumes it exactly where it was.
5. **Forgiving.** Undo the last tap with one button. Fix any tally afterwards in the game review. Show a clear "Saved" state.
6. **Calm and professional.** Club colours used with restraint: a strong primary for headers and key actions, and neutral surfaces. Use the system font (SF Pro), consistent spacing, and real icons (SF Symbols or an Ionicons-style set), never emoji. Don't use gradients, novelty illustrations or generic template looks. It should feel like an official association tool.
7. **Ten-second learnability.** A parent handed the phone mid-game should understand it. Use plain netball language, not stats-nerd jargon.

## Branding (sampled from the Western Districts website, confirmed by Kyllian's screenshot)
| Role | Hex | Where it comes from |
|---|---|---|
| Primary: Wests maroon | `#910B00` | Site header, nav text, headings |
| Charcoal | `#2B3136` | Top bar; use for body text and dark surfaces |
| Blush | `#EFDAD9` | Nav band; use for subtle tinted backgrounds and selected rows |
| Accent: bottlebrush red | `#ED1B2F` | Flower in the logo; use sparingly (live/recording indicator, key highlights) |
| Accent: lime | `#A2C760` | "EST. 1974" in the logo; use sparingly (e.g. "on court" state) |
| Surface | `#FFFFFF` | Page background |

- Maroon on white has a contrast ratio of roughly 9:1 (AAA), so it is safe for text and buttons. Don't put lime text on white because the contrast is too low. Use lime only as a fill with charcoal text, or as an icon tint.
- Positive and negative stats must not depend on red versus green alone. Pair them with an icon or sign as well.
- Keep every colour in one theme file so a single edit re-skins the app.
- Don't use the black-and-red scheme of Waverley District Netball Association, which is a different WDNA.
- The logo (black-and-white netball ring with a red bottlebrush and "EST. 1974") belongs to the association. Don't recreate or trace it. Use a neutral "Wests Netball Coach" wordmark, plus a slot the coach can drop the official logo file into if the association gives permission.

## Netball model
- 7 positions: GS, GA, WA, C, WD, GD, GK. Bench is unlimited.
- Quarters are configurable. The default is 4 × 15 min, and juniors often play 4 × 10 min. Track the quarter for every stat event.
- Positions can change between quarters (and occasionally mid-quarter). Record a substitution or position change as a timestamped event.
- Court time per player = sum of on-court intervals. Show each player's share against an even split (total game time × 7 ÷ squad size) and highlight whoever is furthest below it.

## Default stats (based on the Champion Data glossary)
Positive:
- Goal
- Missed shot (neutral: used to show shooting %)
- Feed (into the circle)
- Goal assist
- Centre pass receive
- Intercept
- Deflection
- Rebound
- Pick-up / gain

Negative:
- Stepping (footwork)
- Bad pass
- Bad hands / dropped
- Held ball
- Offside / over a third
- Contact
- Obstruction
- Centre pass not converted

Coaches can add, rename, hide and reorder stats. Stats are per team and reusable.

## Lessons from existing apps
- Great Coach Netball ($19.99): it has per-quarter positions, planned rotations, minimum game-time alerts, per-quarter notes and undo. Its main complaint is buttons glitching mid-game, so reliability beats features.
- Netball Buddy: it enlarged its stat buttons after feedback. Its fixes were about keeping substitutions when a match is reopened and stopping a player appearing in two positions at once. Copy those lessons.
- Gaps in both that this app fills: it ties into the season calendar, training reflections, drill-sheet photos, and the next session view.

## Budget: $0
- Expo + Expo Go to run on the iPhone. Free, no Apple account needed for development.
- Everything is stored on the device (SQLite/AsyncStorage). No servers, no paid APIs, no accounts.
- Use open-source libraries only (MIT/Apache).
- Later costs, flagged and not needed now: Apple Developer Program (about AUD $149/yr) for TestFlight/App Store, and for a home screen widget on a phone beyond a 7-day free-provisioning build from a Mac.

## Sources
- Champion Data netball glossary: https://www.championdata.com/glossary/netball/
- Great Coach Netball (App Store): https://apps.apple.com/au/app/great-coach-netball/id1394526615
- Netball Buddy (App Store): https://apps.apple.com/us/app/netball-buddy/id6757435916
- Apple HIG, designing for iOS: https://developer.apple.com/design/human-interface-guidelines/designing-for-ios
- GBNL association list (WDNA location): https://gbnl.majestri.com.au/wspGBNLAssociations.aspx
