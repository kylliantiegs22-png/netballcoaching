# Monetization and Launch Playbook: Netball Coach App

**For:** Kyllian, solo developer and netball coach, Brisbane.
**Written:** 27 November 2026.
**Goal:** go from "free app coaches use" to "recurring revenue from netball clubs and associations", one step at a time, with no funding.

How to use this playbook: work through Steps 1 to 10 in order. Each step ends with a **Done when** check. Don't start the next step until that check passes, or until you've made a deliberate call to skip it.

Conventions:
- Prices are in AUD. They include no GST, because you won't be registered for GST (see Step 5).
- Every market or legal fact links to its source. Anything I couldn't verify is marked **ASSUMPTION:**. Check those before you rely on them.
- Dates for the 2027 season come from `data/rep-calendar-2027.json` (the WDNA 2027 rep calendar).

---

## Read this first: what "the app" is today

When I wrote this (27 Nov 2026), the repo held **no app code**. It holds three things:
- `docs/goal-prompt.md`, the v1 build brief for `/goal`
- `docs/design-brief.md`, the users, design principles, netball model and branding
- `data/rep-calendar-2027.json`, the 123 WDNA 2027 rep events across JSA, SSA and GBNL

So every feature named below is a **v1 spec feature**, not shipped code. When the `/goal` build lands, re-run the audit in Step 1 against the real code (grep commands are included) and fix anything that changed.

The order of the whole plan follows from this. **Build v1 and run a free pilot first, then build the paid parts, then charge.** You can't sell a spec to a club treasurer.

---

## Step 1: Audit the app (features, beneficiaries, gaps that block charging)

### Why it matters
Clubs don't pay for features. They pay to make a committee problem go away, such as "parents are complaining about court time" or "every new coach starts from zero." The audit tells you which v1 features already solve a committee problem, and which ones only help a single coach. Coaches will happily use those but won't make a club pay for them.

### What to do
1. List every v1 feature, using the table below as your starting point.
2. For each feature, mark who benefits: player, coach, team manager or club committee.
3. List the gaps that stop you sending an invoice.
4. Once code exists, verify each row with the grep commands at the end of this step.

### Worked example: the v1 feature audit

Source: `docs/goal-prompt.md` (features list) and `docs/design-brief.md`.

| v1 feature (spec) | Player | Coach | Team manager | Club committee | Paid-tier value? |
|---|---|---|---|---|---|
| Season calendar seeded from the 2027 rep calendar (JSA, SSA, GBNL), filterable, editable | | ✔ | ✔ | ✔ (rep coordinator) | **High.** The association publishes one calendar and every rep coach gets it. |
| "Next up" card on home (date, time, team, type, focus) | | ✔ | ✔ | | Low (coach convenience) |
| Day-before local reminders, time adjustable | | ✔ | ✔ | | Low |
| Session archive: photo of paper drill sheet plus notes | | ✔ | | ✔ (coaching coordinator) | **Medium.** Becomes a club drill library if shared. |
| Post-training reflection: went well, work on, 1–5 rating | | ✔ | | ✔ (coach development) | Medium |
| Share a training plan photo via the iOS share sheet | | ✔ | ✔ | | Low |
| Team roster; positions per quarter; changes logged as timestamped events | ✔ (indirect) | ✔ | ✔ | | Medium |
| Live stats: tap player, tap stat, +1, undo; default positive and negative stats from the Champion Data glossary; custom stats; totals by quarter | ✔ (feedback) | ✔ | | | Medium for coaches; low for committees on its own |
| In-game per-player visual showing who's struggling (e.g. too much stepping) | ✔ (indirect) | ✔ | | | Low for committees |
| Court time: running timers, On/Off, even-share indicator, survives lock, background or kill | ✔ | ✔ | ✔ | ✔ | **High.** Fair game time is a junior-netball flashpoint. |
| Game archive by date: stats, court times, notes for next training | ✔ | ✔ | | ✔ (if exported) | **High** once it becomes a season report |
| Wests colours in one theme file; neutral wordmark; logo slot | | | | ✔ | **High.** "Your club's colours" is a reason to buy a club licence. |

Explicitly **not** in v1 (from the goal prompt's SCOPE section): home-screen widget, email or SMS lists, accounts, cloud sync, Android polish, App Store submission.

### Who benefits, in one line each
- **Players:** fairer court time and specific feedback ("3 intercepts in Q2"). They don't use the app.
- **Coaches:** run the whole season from one phone, sideline-safe. This is the free user and your distribution.
- **Team managers:** calendar, reminders and court time. They're often a parent holding the phone (design principle 7: "A parent handed the phone mid-game should understand it").
- **Club committees (president, secretary, coaching coordinator, treasurer):** evidence of fair court time, continuity when volunteer coaches change each season, consistent coaching across teams, and their club's look. These people pay.

### Gaps that block charging (input to Step 4)
1. **No accounts and no sync.** Data lives on one phone. A club can't "own" anything, so there's nothing to license.
2. **No club or team hierarchy.** v1 is one coach's world. A club licence needs Club → Teams → Coaches.
3. **No entitlement check.** Nothing turns club features on or off.
4. **No committee-facing output.** Committees never open the app. They need an end-of-season report and a court-time fairness summary they can read.
5. **Single-club branding.** It's hard-wired to Wests (maroon `#910B00`). Selling to Pine Rivers or Redlands needs per-club theming and a neutral app name.
6. **iPhone only.** Android polish is out of scope in v1. **ASSUMPTION:** a meaningful share of volunteer coaches use Android phones. Check this in the pilot (Step 2 asks).
7. **Children's data handling** isn't written down: no privacy policy, no retention rules, no deletion flow.
8. **Not in the App Store.** You need TestFlight at minimum to put it on pilot coaches' phones.

### Verify against code once it exists
Run these from the repo root after the `/goal` build is pushed. Each line should return hits. If one doesn't, the feature didn't ship, so update the table.

```bash
grep -rniE "rep-calendar-2027|seed" --include=*.ts* src app 2>/dev/null | head   # calendar seeding
grep -rniE "next ?up|nextSession" --include=*.ts* . | grep -v node_modules | head    # Next up card + pure module
grep -rniE "scheduleNotification|notifications" --include=*.ts* . | grep -v node_modules | head
grep -rniE "ImagePicker|camera" --include=*.ts* . | grep -v node_modules | head      # drill-sheet photos
grep -rniE "undo" --include=*.ts* . | grep -v node_modules | head                    # stat undo
grep -rniE "courtTime|onCourt|startedAt" --include=*.ts* . | grep -v node_modules | head
grep -rniE "#910B00" . | grep -v node_modules | head                                 # single theme file
```

### Cost and time
$0. About 2 hours once code exists. Today, 30 minutes to confirm this table against the spec.

### Done when
- Every row in the table is confirmed by a grep hit in real code, or marked "not shipped".
- You can say in one sentence which three features a committee would pay for. (My answer: **shared season calendar, court-time fairness evidence, and season handover/reporting in club colours**.)

---

## Step 2: Validate demand with a free pilot (1–3 local clubs)

### Why it matters
Coaches saying "this is great" costs them nothing. A committee minuting "we'll pay $X for 2027" is the only signal that counts. The pilot is how you earn the right to ask for that.

### What to do
1. **Pick pilot sites where you have warm access.** You coach in the Western Districts Netball Association (WDNA, Faulkner Park, Graceville). Your best first pilot is the **WDNA rep program**, because the app already ships its 2027 calendar. Add one or two clubs that play in the WDNA competition.
   - Brisbane has plenty of room after that. Netball Queensland reports "over 100,000 members", "79 Associations and over 300 clubs across the state" ([Netball Queensland](https://qld.netball.com.au/nqld-membership)). 21 associations played in the 2026 GBNL competition, including Brisbane, Metropolitan Districts, Downey Park, Pine Rivers, Redlands, Ipswich, Goodna, Cannon Hill Districts and Underwood Park ([GBNL associations](https://gbnl.majestri.com.au/wspGBNLAssociations.aspx)).
2. **Time it to the rep season you already have in the data.** SSA compulsory training starts **1 Feb 2027**, with the DPNA SSA Twilight Carnival on **20 Feb**, the WDNA SSA Carnival on **7 Mar** and the NQ SSA Carnival on **3–6 Apr**. GBNL trials are **31 Jan**, with Round 1 on **15 Apr 2027**. JSA compulsory training starts **1 Mar 2027**. A pilot running **1 Feb – 15 Mar 2027** covers SSA training plus two carnivals, which is a real stress test.
3. **Run it with 4–8 coaches in total.** That's enough to see a pattern and few enough to support personally.
4. **Measure without building analytics.** v1 stores everything on the phone and has no telemetry (on purpose). Measure like this instead:
   - A **weekly 3-question form** (free Google Form) every Sunday night: "How many games/trainings did you use it for this week?", "What annoyed you?", "What did you show someone else?"
   - **A screenshot of the game archive list** from each coach in weeks 2, 4 and 6. It's honest data without collecting anything.
   - **Sideline observation:** stand behind two pilot coaches at one game each. Count missed taps and undo presses, and note any moment they looked lost.
   - **Ask once, at week 4:** "How would you feel if you could no longer use the app?" (Very disappointed / Somewhat / Not). Also ask: "iPhone or Android?"
5. **Ask the committee question at week 5–6.** Use the price card from Step 3. Don't ask "would you pay?" Ask "can we put the founding-club price on the agenda for your next committee meeting?"

### Success thresholds (decide these now, before the data comes in)
**ASSUMPTION:** these thresholds are mentor rules of thumb, not industry benchmarks. Treat them as your own bar.

| Metric | Pass | Kill or rethink |
|---|---|---|
| Activation: coaches who track a full game within 14 days of install | ≥ 70% | < 40% |
| Retention: coaches still using it for ≥ 3 of their last 4 games/trainings in week 6 | ≥ 50% | < 25% |
| "Very disappointed" if it went away (week 4) | ≥ 40% of coaches | < 20% |
| Data-loss incidents (anything lost after a lock, kill or call) | 0 | Any. Stop and fix first. |
| Committee pull: club or association agrees **in writing** to the founding price | ≥ 1 | 0 after 3 committee conversations |

### Worked example: the pilot ask (sideline or coffee, WDNA)
> "I've built an app for coaching our rep teams. It already has the whole 2027 WDNA rep calendar in it, it tracks court time so every girl gets her fair share, and it keeps stats with two taps. I'd like four of our SSA and GBNL coaches to use it free from the first SSA training on 1 February through to the WDNA carnival on 7 March. All I ask is a 2-minute form each Sunday, and that you'll tell me honestly if it's rubbish. Can I show you on my phone right now?"

Then show three things only: Next up, a live court-time screen, and the game archive.

**Conflict of interest:** you're a Wests coach pitching Wests. Say so plainly at the start of any committee conversation, and offer to leave the room for the vote. Committees respect it, and it protects you.

### Cost and time
- $0, plus TestFlight access. TestFlight needs the Apple Developer Program, which costs "99 USD (or in local currency where available)" a year ([Apple fee-waiver page](https://developer.apple.com/help/account/membership/fee-waivers/)). **ASSUMPTION:** that's about A$149 in Australia. Check at enrolment.
- About 3 hours a week for 6 weeks: onboarding, Sunday form review, one sideline visit, fixes.

### Done when
- The pilot has run at least 6 weeks with at least 4 coaches.
- You have a filled-in thresholds table with real numbers.
- At least one written "yes, put it on the agenda", or you've gone to the "If demand fails" plan in Step 10.

---

## Step 3: Pricing model

### Why it matters
Community clubs budget once a year, run on volunteer treasurers, and fear surprise bills. Per-coach monthly subscriptions die in committee. One annual club price with a clear team limit gets approved in a single meeting.

### What competitors charge (this is what clubs compare you to)

| Product | What clubs or coaches pay | What it covers | Source |
|---|---|---|---|
| **Team App** | "Completely free"; ad-funded; pay to remove ads | Club comms, events, members, store | [Team App FAQ](https://support.teamapp.com/en/knowledgebase/5-general-faqs/docs/97-cost-of-team-app) |
| **Spond** | Free app; Spond Club payments cost AUD 2.5% + $0.30 per transaction, covered by the club by default | Team comms, events, payments | [Spond (free)](https://help.spond.com/en/articles/25998-spond-is-free-to-download-and-use), [Spond Club fees](https://help.spond.com/club/en/articles/58192-what-is-the-transaction-fee-in-spond-club) |
| **PlayHQ** | The association's registration platform. Netball Australia says it "was implemented nationwide in December 2021" | Registration, payments, fixtures, ladders, live scoring, game-day line-ups, player stats for fans | [Netball Australia](https://netball.com.au/playhq), [PlayHQ netball](https://playhq.com/au/for-your-sport/netball) |
| **Great Coach Netball** | A$19.99 one-off, per coach | Interchange planning, player time, stats, match reports | [App Store AU](https://apps.apple.com/au/app/great-coach-netball/id1394526615) |
| **Elevate Coach (NetSpirit content)** | Free download; "Single User – Yearly $79.99" | Session plans, coach education, video library | [App Store AU](https://apps.apple.com/au/app/netspirit/id1579273954) |
| **Netball Buddy** | Free with ads; Pro US$1.99/month or US$14.99/year | Live stats; Pro unlocks unlimited teams and analytics | [App Store US](https://apps.apple.com/us/app/netball-buddy/id6757435916) |
| **Squad: Netball** | Free; £3.49/month or £35.99/year (UK store) | Player training, drills, match stats | [App Store GB](https://apps.apple.com/gb/app/squad-netball/id1567153792) |

**ASSUMPTION:** I couldn't confirm whether Queensland associations run on PlayHQ. The Netball Australia rollout page lists other states, and GBNL runs on a Majestri site. Ask your association secretary which platform they use.

**What this tells you:**
1. **Don't compete with Team App, Spond or PlayHQ.** Comms, registration and payments are free or already mandated. Never pitch "replace Team App."
2. **Coach tools are priced per coach and cheaply** (A$19.99 one-off to A$79.99 a year), and none of them sells a **club** view: shared calendar, court-time fairness across all teams, handover between seasons, club colours. That gap is your product.
3. A price per **club** beats a price per coach. A club with 15 teams paying A$79.99 per coach for Elevate would spend about A$1,200. Your club price looks cheap next to that, while being a lot of money for you.

### Recommendation: free coach app plus an annual club licence

| Plan | Price (AUD, per year, no GST) | Who it's for | What's included |
|---|---|---|---|
| **Coach (free forever)** | $0 | Any coach | 1 team; every v1 game-day feature (stats, undo, court time, calendar, reminders, drill photos, reflections, archive); data stays on the phone; manual export file |
| **Club Small** | **$199** | Clubs with up to 6 teams | Everything in Coach, for every coach in the club, plus a shared club calendar, coach invites, club colours and logo, end-of-season team reports (stats and court-time share), season handover to next year's coach, cloud backup |
| **Club** | **$449** | Clubs with up to 20 teams (each extra team $20) | Club Small, plus a coaching-coordinator view across all teams and a club drill library from shared session photos |
| **Association / Rep Program** | **$899** | An association's rep program (JSA, SSA, GBNL), up to 40 teams | Club, plus the association's rep calendar preloaded for every rep coach, association colours, and a season report across all rep teams for the rep committee |

**Founding-club offer (first 5 clubs only):** 50% off the first year, with the price locked for 2027 and 2028. In return, the club gives a 20-minute feedback call each month, and a quote you can use publicly **only if they choose to give one**.

**Free-tier limit, and why it's there:** one team per coach and no cloud sync. A single coach never hits a paywall in the middle of a game, which matters because the design brief promises "nothing ever lost". The limit only bites when a club wants to coordinate several teams, which is exactly when the club should pay.

### Reasoning behind the numbers
- **Per team:** Club at $449 for 20 teams is **$22.45 per team per year**, about the price of one Great Coach Netball licence, but shared across every coach in the team and the committee.
- **Per player:** **ASSUMPTION:** junior squads usually carry about 9–10 players (7 on court plus bench). That puts 20 teams at roughly 190 players, or **about $2.35 per player per year**. Say it exactly that way in the committee.
- **Grant cover:** the Queensland Active Clubs program has funded "up to $2,000 (GST exclusive)", including "resources or technology to support coaching staff ... (including coaching manuals/aids, software)" ([Active Clubs](https://www.qld.gov.au/recreation/sports/funding/active-clubs)). **ASSUMPTION:** that page refers to a 2019 round, so I couldn't confirm a current round. Check [Queensland sport funding](https://www.qld.gov.au/recreation/sports/funding) for open programs before you mention grants to a club.
- **Annual billing only.** Treasurers pay once a year. It matches the season, and it saves you chasing 12 payments.
- **ASSUMPTION:** I couldn't verify how much community clubs are willing to pay. The pilot's committee ask in Step 2 is the test. If three committees say no to $449, drop the Club price to $299 before you change anything else.

### Worked example: your pricing page (paste into a one-page site)

> # Fair court time, a shared season, and every stat, for your whole club
>
> **Coach — Free forever**
> One team. Live stats in two taps, court-time timers that survive a locked phone, your season calendar, drill-sheet photos and post-training notes. No account needed.
>
> **Club Small — $199 a year** · up to 6 teams
> Every coach in your club on the same calendar, in your club colours. End-of-season reports showing each team's stats and court-time share. When a coach moves on, next year's coach starts with the history.
>
> **Club — $449 a year** · up to 20 teams (+$20 per extra team)
> Everything in Club Small, plus a coaching-coordinator view across every team and a shared drill library.
>
> **Association / Rep Program — $899 a year** · up to 40 teams
> Your rep calendar preloaded for every JSA, SSA and GBNL coach, and a season report for your rep committee.
>
> *Founding clubs: the first five clubs get 50% off 2027, with the price locked for 2028.*
> Prices in AUD. No GST is charged. Paid yearly by invoice (bank transfer or card).
> **Book a 15-minute demo:** [your email]

### Cost and time
$0. About 1 hour to write the page on a free host (for example GitHub Pages from this repo). **ASSUMPTION:** a `.com.au` domain costs roughly A$20–30 a year, and it's optional at this stage.

### Done when
- The price card fits on one page, and a club treasurer you know can repeat it back after 30 seconds.
- You've written down the single number you'll drop to ($299 Club) if committees refuse, so you don't discount on the spot.

---

## Step 4: Minimum product changes needed to charge (planned, not built)

### Why it matters
Each item below is the smallest change that turns a club licence into something real. Build in this order and stop at P0 until the first invoice is paid.

### Where it lands in the codebase
No code exists yet. The goal prompt prescribes this structure:
- domain logic (stats, timers, schedule) kept apart from screens
- a **pure next-session module** a widget can reuse
- **one theme file** for colours
- storage on the phone (the design brief names SQLite or AsyncStorage)
- calendar seeding from `data/rep-calendar-2027.json`
- a decisions log

The mapping below refers to those modules by role. **ASSUMPTION:** exact paths depend on what `/goal` generates, so re-map each row once code lands.

### The prioritised list

| # | Change | Why it blocks charging | Maps to | Rough effort (solo) |
|---|---|---|---|---|
| **P0-1** | **Club and team IDs on every record.** Add `clubId` and `teamId` to roster, game, stat event, court-time interval, session and reflection records. Write a migration for existing single-coach data (assign a default personal club). | Without this, nothing can belong to a club | Storage schema plus domain types (stats, timers, schedule modules) | 2–3 days |
| **P0-2** | **Sign-in (email magic link), only for club features.** The free Coach plan stays account-free. | A licence needs to know who's in the club | New auth module; settings screen | 2–4 days |
| **P0-3** | **Hosted backend for club data.** Store club, teams, members, the club calendar and game summaries. Live stat taps stay local-first (design principle 4) and sync after the game. | Shared calendar, backup and handover all need a server | New sync module next to storage; domain logic unchanged | 5–8 days |
| **P0-4** | **Roles and invites.** Club admin and coach roles. A 6-character team invite code the admin shares by text. | Club controls who sees its players' data | Auth plus backend rules | 2 days |
| **P0-5** | **Entitlement check.** The club record holds `plan`, `teamLimit` and `paidUntil`. The app reads it and turns club features on. There's **no purchase button or price in the iOS app** (see Step 6). A "Club licence" screen shows status and "Ask your club admin." | Turns payment into access | New entitlement module; settings screen; feature flags around club screens | 1–2 days |
| **P0-6** | **Children's data minimum** (see Step 5). Store player first name plus last initial by default, with no date of birth, phone or email for players. Warn before attaching photos showing children (drill-sheet photos only). Add "Delete team", "Delete season" and in-app account deletion, a privacy policy link, and a club data export (CSV). **ASSUMPTION:** Apple requires in-app account deletion for apps that offer account creation (App Review Guideline 5.1.1(v)). Confirm in the current guidelines. | Committees now operate under Child Safe Standards (Step 5) and will ask | Roster domain model; settings; backend | 2–3 days |
| **P1-1** | **Per-club theme.** Load colours and logo from the club record into the existing single theme file's structure. Default to neutral when no club is set. | "Your club's colours" is the visible reason to buy | Theme file | 1–2 days |
| **P1-2** | **End-of-season team report (PDF or share sheet).** Totals by player and quarter, court-time share against the even split (the brief's formula: total game time × 7 ÷ squad size), and coach notes. | The committee's deliverable and renewal evidence | New report module reading the game archive | 2–3 days |
| **P1-3** | **Club calendar import.** Generalise the rep-calendar seeding to accept a club's CSV (date, team, type, venue). | Lets any club, not just WDNA, get the "calendar already in it" moment | Calendar seed module | 1–2 days |
| **P1-4** | **Neutral app name and Android build.** Expo builds Android from the same code. Polish only the game-day screens. | Opens non-Wests clubs and Android coaches | App config; theme default | 2–4 days |
| **P2** | Individual "Coach Pro" in-app purchase; parent read-only view; home-screen widget | Only if Step 10's fallbacks trigger | — | Later |

**Total P0:** about 14–22 working days. **ASSUMPTION:** these estimates assume Claude Code does most of the typing and you review and test. Double them if you're doing evenings only.

### Worked example: what the first paying club sees
Pine Rivers-style club, 8 teams, Club plan:
1. The club secretary pays the invoice.
2. You set the club's `plan=club`, `teamLimit=20` and `paidUntil=2028-01-31` by hand in the backend dashboard. No billing code yet.
3. The secretary signs in on their phone and creates 8 teams. They text each coach a code such as `PRN4QK`.
4. Each coach opens the free app and taps **Join club**. The club calendar and club colours appear. Their existing team data is attached to the club.
5. In September, the coaching coordinator exports 8 season reports for the AGM.

### Cost and time
- **Backend: ASSUMPTION:** hosted backends such as Supabase or Firebase have free tiers that cover pilot-scale use. Budget up to about A$40 a month once paying clubs are live, and check current limits before choosing.
- **Time:** 3–5 weeks of focused build for P0, plus 2 weeks for P1.

### Done when
- One test club with 2 teams and 2 coaches on 2 different phones can join, share the calendar, run a game offline, sync afterwards, and delete a team.
- The free single-coach flow still works with no sign-in.
- No price or buy button appears anywhere in the iOS app.

---

## Step 5: Business and legal setup in Australia

### Why it matters
A treasurer won't pay an invoice without an ABN. A committee won't put children's names into an app without a privacy policy. Both are cheap to fix and expensive to skip.

### What to do, in order

1. **Get an ABN as a sole trader.** "Registering for an ABN is free" ([Business Registration Service](https://register.business.gov.au/)). It takes about 20 minutes. **ASSUMPTION:** as a sole trader you report this income on your personal tax return. Confirm with a tax agent or the ATO.
2. **Decide on a business name.** You only need to register one if you trade under a name other than your own. ASIC charges a fee for 1 or 3 years ([ASIC](https://www.asic.gov.au/for-business-and-companies/business-names/register-a-business-name/how-to-register-a-business-name-with-asic/)). **ASSUMPTION:** the page didn't show the amounts, but it's roughly A$45 for 1 year. Before choosing the app's public name, search the IP Australia trade mark search and the App Store for clashes.
3. **Open a separate bank account** for app income and costs. **ASSUMPTION:** several Australian banks offer business accounts with no monthly fee.
4. **Don't register for GST yet.** You must register when GST turnover reaches **$75,000** (current or projected over 12 months), within 21 days. If you register voluntarily you generally stay registered for at least 12 months ([ATO](https://www.ato.gov.au/businesses-and-organisations/gst-excise-and-indirect-taxes/gst/registering-for-gst)). Every scenario in Step 9 sits far below $75,000. **ASSUMPTION:** while unregistered, issue a plain "invoice" (not a "tax invoice") and state "No GST has been charged."
5. **Privacy Act position.** Small businesses with annual turnover of **$3 million or less** are generally exempt, unless, for example, they're a health service provider or trade in personal information. Small businesses can also opt in ([OAIC small business](https://www.oaic.gov.au/privacy/privacy-for-organisations/small-business)).
   - **Recommendation: behave as if you're covered.** Follow the Australian Privacy Principles and consider opting in. Your customers are clubs holding children's data, and "we follow the Privacy Act even though we're exempt" closes deals.
6. **Children's Online Privacy Code.** The OAIC must register this code by **10 December 2026**. It applies to Privacy Act-covered entities whose online service is "likely to be accessed by children" or primarily concerns children's activities ([OAIC code page](https://www.oaic.gov.au/privacy/privacy-registers/privacy-codes/childrens-online-privacy-code)).
   - The exposure draft proposes: handling consistent with the child's best interests, privacy-protective defaults, age assurance, parental consent for under-15s, consents lasting no more than 12 months, and a right to have data destroyed ([OAIC exposure draft release](https://www.oaic.gov.au/news/media-centre/oaic-releases-exposure-draft-of-the-childrens-online-privacy-code); [Allens analysis](https://www.allens.com.au/insights-news/insights/2026/04/draft-childrens-online-privacy-code-proposed-protections-to-have-material-impact-on-online-services/)).
   - **ASSUMPTION:** while you're exempt and the app is used by adult coaches, the code likely doesn't bind you directly. But the app records data about children, and **teenage assistant coaches may use it**. So design to the draft anyway: minimal data by default (P0-6), no direct marketing, and a destroy-on-request flow. Re-check the final code after 10 Dec 2026.
7. **Child safety (Queensland).** Under Queensland's Child Safe Organisations Act 2024, sport and active recreation organisations had to **commence** compliance with the Child Safe Standards by **1 April 2026** (1 Jan 2026 for clubs running overnight camps). Blue cards are a separate requirement for people working with children ([Queensland Government, keeping sport safe](https://www.qld.gov.au/recreation/sports/club-support/keeping-sport-and-recreation-safe)).
   - **What this means for you:** your customers now have formal child-safe obligations. Write a one-page **"Child safety and privacy fact sheet"** for committees covering what data the app stores, where it's stored, who can see it, how to delete it, and that you never contact players.
   - **ASSUMPTION:** as a software supplier who doesn't work directly with children, you don't need a blue card for the business. You likely already hold one as a coach.
8. **Write three documents:**
   - **Privacy policy:** what's collected (coach email; player first name and initial; stats; court time; photos the coach adds), purpose, storage location, retention (deleted 30 days after a club cancels), access, and a contact.
   - **Terms of use** for the free app.
   - **Club licence agreement (1 page):** the club owns its data; annual term; renewal notice 30 days before; you delete data on request; liability capped at fees paid.
   - **ASSUMPTION:** a lawyer review of these costs roughly A$500–1,500. Optional before the first five clubs, recommended before 20.
9. **Store and payment fees** (inputs to Steps 6 and 9):
   - **Apple:** the Small Business Program cuts commission to **15%** on paid apps and in-app purchases for developers with up to US$1 million in proceeds ([Apple](https://developer.apple.com/app-store/small-business-program/)).
   - **Google Play (Australia):** auto-renewing subscriptions cost **10% plus a 5% billing fee**, with regional rates effective 30 Sep 2026 for Australia ([Google Play Console Help](https://support.google.com/googleplay/android-developer/answer/112622?hl=en)).
   - **Stripe (Australia):** **1.7% + A$0.30** for standard domestic cards and 3.5% + A$0.30 for international cards. Stripe Invoicing is 0.4% per paid invoice (one-off invoices capped at $2), and Stripe Billing is 0.7% of billing volume ([Wise summary of Stripe AU pricing](https://wise.com/au/blog/stripe-fees)).
   - **Insurance: ASSUMPTION:** professional indemnity or cyber cover for a micro software business costs roughly A$500–1,000 a year. It's optional at pilot stage. Get two quotes before 10 clubs.

### Worked example: the fact sheet header you hand a committee
> **[App name] — Child safety and privacy, in one page**
> - The app stores each player's **first name and last initial only**. No birthdays, phone numbers, emails or photos of players.
> - Game stats and court time are stored on the coach's phone. Club data (calendar, rosters, season summaries) is backed up to [provider], hosted in [region].
> - Only coaches your club admin invites can see your teams. Remove a coach and their access ends.
> - Your club owns its data. Ask and we delete it within 30 days.
> - The app never contacts players or parents and shows no advertising.
> - Operated by [your name], ABN [number], Brisbane. I follow the Australian Privacy Principles.

### Cost and time
- ABN $0. Business name optional (**ASSUMPTION:** about A$45 a year). Bank account $0 (**ASSUMPTION**). Documents $0 DIY or about A$500–1,500 with a lawyer (**ASSUMPTION**).
- About 1 day in total, spread over Weeks 1–3.

### Done when
- You have an ABN, an invoice template showing it, and a separate account for receiving payment.
- The privacy policy is live at a public URL and linked in the app's settings.
- The one-page fact sheet exists as a PDF.

---

## Step 6: Payments implementation choice

### Why it matters
The wrong choice either gives Apple 15% for nothing or gets your app rejected. You're selling a licence **to organisations**, which changes the rules.

### The rules that decide it
Apple's App Review Guidelines ([Apple](https://developer.apple.com/app-store/review/guidelines/)):
- **3.1.1:** unlocking features or functionality within the app requires in-app purchase.
- **3.1.3(b), multiplatform services:** you may honour purchases made elsewhere, but those items must also be available as in-app purchases.
- **3.1.3(c), enterprise services:** "If your app is only sold directly by you to organisations or groups for their employees or students", you may use other purchasing methods. "Consumer, single-user or family sales must use in-app purchase."

**ASSUMPTION:** the guideline text above came through as a summary when I fetched it. Read the current wording yourself before you submit.

On Australian court action:
- The Federal Court ordered Apple to allow alternative payments and links in Australia. Those orders commence **1 March 2027** at the earliest, are under a limited stay, and Apple says it will appeal ([Gilbert + Tobin](https://www.gtlaw.com.au/insights/federal-court-orders-apple-to-make-changes-to-australian-app-store-payment-restrictions-in-epic-litigation)).
- **Don't build your plan on this.** Treat it as a possible upside later.

### Options compared

| Option | Fees | Fits a club licence? | Effort |
|---|---|---|---|
| **App Store / Play in-app purchase** | 15% (Apple SBP); 10% + 5% (Play subscriptions, AU) | Poorly. IAP is tied to one person's Apple ID, but a club treasurer pays once for 20 coaches. | Medium |
| **Stripe (web invoices, Payment Links, then Billing)** | 1.7% + $0.30 per card payment; invoicing 0.4% (capped $2 one-off); Billing 0.7% | Yes. Treasurers want invoices, and bank transfer is free. | Low |
| **RevenueCat** (on top of IAP) | Free under US$2.5K monthly tracked revenue, then 1% ([RevenueCat](https://www.revenuecat.com/pricing)) | Only matters if you sell individual in-app subscriptions | Low–medium |

### Recommendation for this app (Expo, iPhone first)
1. **The free app in both stores has no purchase screen and no price.** Club features unlock when a coach joins a paid club (P0-5). That's the 3.1.3(c) model: sold directly by you to organisations for their coaches. In App Review notes, write: "Club features are licensed directly to netball clubs and associations for their volunteer coaches. No consumer purchases are offered."
2. **For the first 5–10 clubs:** send an emailed invoice with your ABN and bank details (EFT, $0 fees), plus a Stripe Payment Link as a card option. Turn entitlements on by hand in the backend.
3. **From about 10 clubs:** move to Stripe Invoicing or Billing so renewals and reminders run themselves.
4. **Add RevenueCat plus in-app purchase only if** you launch an individual "Coach Pro" plan (Step 10 fallback) or Apple rejects the 3.1.3(c) approach.

### Worked example: the first invoice
> **INVOICE #0001** · Issued 22 Feb 2027 · Due 21 Mar 2027
> To: Treasurer, [Club name] Netball Club
> From: [Your name], ABN [number], Brisbane QLD
> Club licence, 2027 season (1 Mar 2027 – 28 Feb 2028). Club plan, up to 20 teams. Founding-club price (50% off $449): **$224.50**
> No GST has been charged.
> Pay by EFT: BSB [xxx-xxx] Acc [xxxxxxxx], reference INV0001 · or by card: [Stripe Payment Link]

Fees on that invoice: $0 by EFT. By card, 1.7% × $224.50 + $0.30 = **$4.12** (based on the Stripe rate above).

### Cost and time
- Stripe account $0 to open (**ASSUMPTION:** no monthly fee on standard pricing). About 1 hour to set up Payment Links. The invoice template takes about 30 minutes in any free invoicing tool or a document.

### Done when
- Invoice template is ready; Stripe Payment Link for $224.50 and $449 created (in test mode until the first real club says yes).
- App Review notes text is written and saved in the repo's decisions log.

---

## Step 7: Launch (store listing, screenshots, outreach, social, referral)

### Why it matters
Your launch isn't a press release. It's 20 personal conversations with Brisbane netball committees, with a store listing and social posts that make you look legitimate when they check you out afterwards.

### 7a. Store listing copy (adjust the name after your trade mark check)
- **Name:** [App name]: Netball Coach
- **Subtitle:** Stats, court time, season plan
- **Promotional text:** Run your netball season from one phone. Two-tap stats, fair court time, and your whole calendar, built for the sideline.
- **Description:**
  > Built by a Brisbane netball coach for the sideline: bright sun, night courts, one hand free.
  >
  > • **Live stats in two taps.** Tap a player, tap a stat. Undo anything. Goals, feeds, intercepts, deflections, rebounds, stepping, held ball, contact, obstruction, and your own custom stats. Totals by quarter.
  > • **Fair court time.** Running timers per player with an even-share indicator, so you can see who needs more time. Timers stay correct if your phone locks, a call comes in, or the app closes.
  > • **Your season in one place.** Training and game calendar, a "Next up" card, and a reminder the day before.
  > • **Remember what worked.** Snap your paper drill sheet, add notes, rate the session.
  > • **Game archive.** Every game's stats, court time and notes for next training.
  >
  > Free for coaches. Clubs and associations can add a club licence for a shared calendar, club colours and end-of-season reports.
- No ratings or testimonials until real ones exist.

### 7b. Screenshots plan (6 frames, captured from the real app)
1. Live stats grid mid-Q2, with the headline "Two taps per stat. Eyes stay on the court."
2. Court time screen with one player highlighted below an even share: "See who needs more court time."
3. Next up card on home: "Always know what's next."
4. Calendar filtered to GBNL: "Your whole season, already loaded."
5. Game archive totals: "Every game, saved."
6. Club colours example (P1-1, once built): "In your club's colours."

Use made-up player names (Ava, Mia, Chloe...), never real juniors. **ASSUMPTION:** Apple's required screenshot sizes change over time, so check App Store Connect when you upload.

### 7c. Outreach targets (list-building is a Week 1 task)
Start with WDNA and its clubs. Then go through the 21 GBNL associations ([list](https://gbnl.majestri.com.au/wspGBNLAssociations.aspx)), starting with the ones whose carnivals are already in your calendar data: **DPNA** (Downey Park, which hosts the SSA Twilight Carnival on 20 Feb and the JSA Carnival on 13 Jun) and **MDNA** (Metropolitan Districts, which hosts the Duncan Cup). For each, record: association secretary, rep coordinator, club presidents, and the committee meeting night. Use the contact details they publish on their own websites. Keep the list in a spreadsheet.

### 7d. Outreach scripts

**Email to a club president or secretary (first contact)**
> Subject: Fair court time across all your teams (free trial for [Club] coaches)
>
> Hi [Name],
>
> I'm Kyllian, a netball coach at Western Districts. I've built an iPhone app that our coaches use on the sideline: two-tap stats, court-time timers that show who's below their fair share, and the season calendar already loaded.
>
> I'm offering [Club] free use for your coaches this season, and a founding-club price for a club licence if your committee finds it useful. That's $224.50 for 2027 for up to 20 teams, which works out to about $1.20 per player.
>
> Could I have 10 minutes at your next committee meeting, or 15 minutes on the phone this week? I'll show it on my phone. There are no slides.
>
> Thanks,
> Kyllian
> [phone] · ABN [number] · [website]

**Follow-up (5 working days later, one only)**
> Hi [Name], just bumping this in case it got buried. Even one of your coaches trying it at training this week would tell me a lot. Happy to drop by on a training night at [venue] if that's easier. — Kyllian

**Committee pitch (10 minutes, phone in hand)**
1. (1 min) Declare who you are and any interest: "I coach at Wests. I built this. I'm selling it."
2. (2 min) The problem in their words: "Parents question court time. New coaches start from zero each season."
3. (4 min) Live demo: court-time screen, two-tap stat, season report.
4. (1 min) Pilot evidence: real numbers from Step 2 only, such as "4 SSA coaches, 6 weeks, X games tracked."
5. (1 min) Price card and the founding offer. Mention grants only if you've confirmed a current round.
6. (1 min) The ask: "Can we start your coaches free now, and put the licence on your next agenda?"

**Sideline script (at a carnival)**
> "Are you coaching today? I'm testing a free court-time app with WDNA coaches. Can I show you for 30 seconds after your game?"

### 7e. Social content (4 weeks, one post a week, Facebook and Instagram page)
1. A 15-second screen recording of tapping a stat and pressing undo: "Two taps. Eyes back on the court."
2. A court-time screen with fake names: "Who's had the least court time this half? Now you know without a clipboard."
3. "What we're tracking at the WDNA SSA carnival this weekend": a stat checklist based on the Champion Data glossary ([Champion Data](https://www.championdata.com/glossary/netball/)).
4. Founding-club offer post with a link to the pricing page.

Post into your own coaching networks. **ASSUMPTION:** Brisbane netball coach and club Facebook groups exist and allow posts like this. Read each group's rules first.

### 7f. Referral offer
**"Bring a club, both get $50 off."** When a paying club introduces another club that signs, each gets $50 off its next renewal. This works because committee members sit on association boards and talk at carnivals. Cap it at two referral credits per club per year.

### Cost and time
- Store listing: $0 beyond the developer fee; about 3 hours.
- Screenshots: about 2 hours.
- Outreach list: about 4 hours.
- Emails: about 15 minutes each.
- Social: about 1 hour a week.
- Optional printed A5 flyers for carnivals: **ASSUMPTION:** about A$30 for 100.

### Done when
- The app is live on TestFlight, or in the App Store once you choose public release.
- The pricing page and privacy policy are public.
- 20 named contacts are on the list, and 10 personal emails have been sent.

---

## Step 8: First 90 days (Mon 30 Nov 2026 – Sun 28 Feb 2027)

Targets are cumulative. Cash comes later than signatures: clubs pay in March, for the winter season, at the earliest.

| Week (Mon) | Focus | Key actions | Target by Sunday |
|---|---|---|---|
| **W1 · 30 Nov** | Build v1 | Run `/goal` with `docs/goal-prompt.md`; apply for ABN; enrol in Apple Developer Program; start the 20-contact outreach list | v1 builds; ABN applied |
| **W2 · 7 Dec** | Get it on phones | TestFlight build; use it at your own sessions; draft privacy policy and fact sheet; re-run the Step 1 grep audit | Running on your phone for 2 sessions |
| **W3 · 14 Dec** | Warm asks before Christmas | Coffee or call with the WDNA rep coordinator plus 2 club presidents; recruit 4–8 SSA and GBNL coaches for the February pilot | 3 conversations held; 4 pilot coaches named |
| **W4 · 21 Dec** | Christmas (light week) | Fix bugs; write store listing; pricing page live | Pricing page public |
| **W5 · 28 Dec** | Light week | Start P0-1 (club/team IDs) | P0-1 merged |
| **W6 · 4 Jan** | Build P0 | P0-2/3 (sign-in, backend); send 10 outreach emails to non-Wests clubs | 10 emails sent |
| **W7 · 11 Jan** | Build P0 | P0-4/5 (invites, entitlements); follow-ups; book February committee slots | 2 committee slots booked |
| **W8 · 18 Jan** | Pilot prep | P0-6 (privacy minimum); 15-minute onboarding guide for pilot coaches; Sunday form set up | Onboarding guide done |
| **W9 · 25 Jan** | Pilot onboarding | Install on pilot coaches' phones (GBNL trials 31 Jan) | 4+ coaches installed |
| **W10 · 1 Feb** | Pilot live | SSA induction and compulsory training start 1 Feb; first Sunday form | ≥ 70% tracked a session |
| **W11 · 8 Feb** | Pilot | Sideline visit 1; fix top complaint; JSA squad training continues | 0 data-loss incidents |
| **W12 · 15 Feb** | Pilot plus pitch | GBNL training starts; week-4 "disappointed" question; sideline visit 2 at the DPNA Twilight Carnival (20 Feb) | Survey results in |
| **W13 · 22 Feb** | Close | Committee pitches with real pilot numbers; send founding-club invoices for a 1 Mar start | **2 founding clubs agreed in writing** (expected); 1 invoice sent |

**Target ranges at day 90 (honest version):**
- Conservative: 1 pilot running and 0 clubs agreed.
- Expected: 2 founding clubs or associations agreed, with invoices sent of about $225–450 in total.
- Optimistic: 4 agreed, with about $900 invoiced.

Monthly revenue isn't meaningful yet, because licences are annual. Track **"annual contract value signed"** instead.

**Done when:** the W13 row's target is met or consciously missed, and you've written a one-paragraph review saying which threshold in Step 2 passed or failed.

---

## Step 9: Numbers (2027 season, year 1)

Inputs:
- List prices: $199 Club Small, $449 Club (average **$324** when you assume an even mix), $899 Association.
- Founding clubs (first 5) pay 50%, so **$162** on average.

**ASSUMPTION:** these are scenarios to plan against, not forecasts. No club has agreed to anything yet.

| | Conservative | Expected | Optimistic |
|---|---|---|---|
| Founding clubs (50% off) | 2 → **$324** | 5 → **$810** | 5 → **$810** |
| Full-price clubs | 0 | 3 → **$972** | 15 → **$4,860** |
| Association rep programs | 0 | 1 → **$899** | 3 → **$2,697** |
| **Revenue (year 1)** | **$324** | **$2,681** | **$8,367** |
| Apple Developer Program (**ASSUMPTION:** ≈ A$149) | $149 | $149 | $149 |
| Google Play one-off registration (**ASSUMPTION:** US$25 ≈ A$40) | $0 (iPhone only) | $40 | $40 |
| Domain (**ASSUMPTION**) | $30 | $30 | $30 |
| Backend (**ASSUMPTION:** free tier; then ≈ A$40/month from April) | $0 | $360 | $360 |
| Payment fees (half paid by card at 1.7% + $0.30) | ≈ $3 | ≈ $25 | ≈ $75 |
| Lawyer review of documents (**ASSUMPTION**) | $0 | $0 | $1,000 |
| Insurance (**ASSUMPTION**) | $0 | $0 | $750 |
| **Costs** | **≈ $182** | **≈ $604** | **≈ $2,404** |
| **Net (before your time)** | **≈ +$142** | **≈ +$2,077** | **≈ +$5,963** |
| Share of Queensland's 300+ clubs ([NQ](https://qld.netball.com.au/nqld-membership)) | < 1% | ≈ 3% | ≈ 8% |

**Year 2 (2028):** founding clubs keep their locked price. **ASSUMPTION:** 80% of clubs renew. With that, the expected case renews about $2,145 before any new sales. That's why renewals, and the end-of-season report that justifies them, matter more than new-club count.

**Your time is the real cost.** The expected case is about 25 days of build plus about 4 hours a week of selling and support through the season. Decide now whether about $2,000 in year 1 is worth that to you as a stepping stone, rather than as income.

**GST check:** every case stays far below the $75,000 threshold ([ATO](https://www.ato.gov.au/businesses-and-organisations/gst-excise-and-indirect-taxes/gst/registering-for-gst)).

### Done when
- You've copied this table into a spreadsheet with your real numbers in a fourth column.
- You've updated that column on the last Sunday of every month.

---

## Step 10: Risks, and what to do if a step fails

| Risk or failure | Early warning | What to do |
|---|---|---|
| **Data lost mid-game** (the top complaint about competitor apps in the design brief) | Any pilot report of a missing tally or court time | Stop selling. Fix it and add a regression test for that exact sequence. Tell affected coaches personally. Reliability beats features. |
| **Pilot coaches stop using it** (retention < 25%) | Sunday forms go quiet in week 3 | Sit with two coaches at a game and ask what's slower than paper. Cut features to the game-day core. Re-pilot with JSA from 1 Mar. |
| **Committees won't pay** (0 of 3 say yes) | "Love it, no budget" | 1) Drop Club to $299. 2) Pitch associations' rep programs instead of clubs, since they have one decision-maker and bigger budgets. 3) Fall back to an individual **Coach Pro** in-app subscription via RevenueCat. **ASSUMPTION:** about A$4.99 a month or A$39.99 a year, set between Great Coach's $19.99 and Elevate's $79.99. |
| **Apple rejects the 3.1.3(c) approach** | App Review cites 3.1.1 | Reply with the enterprise explanation once. If it's still rejected, add an in-app "Team Pass" via RevenueCat at the same price, priced so the 15% cut is covered (for example $229 instead of $199), and keep invoices for clubs paying outside the app. |
| **Most coaches are on Android** | Week-4 "iPhone or Android?" answer | Pull P1-4 forward. Expo builds Android from the same code. Use the web build as a stopgap for the pilot. |
| **Privacy incident or a parent complaint** | Any question about a child's data | Use the fact sheet. Delete on request within 30 days. Keep the default of first name plus initial only. Report notifiable breaches as the law requires once you're covered. |
| **PlayHQ or an association platform adds coach stats** | Release notes or association emails | Lean into what it won't do: training reflections, drill-sheet archive, club colours, rep calendar handover. Integrate rather than fight. |
| **Conflict of interest at Wests** | Anyone asks "is Kyllian making money from us?" | Declare it in writing, abstain from the vote, and charge Wests the same founding price as everyone else. |
| **Seasonality: no cash until March** | Expected | Keep costs on free tiers until the first invoice is paid. Don't upgrade the backend plan before then. |
| **Burnout (solo, volunteer coach, developer)** | Missed Sunday reviews | Cap selling at 4 hours a week. The 90-day plan has two light weeks over Christmas on purpose. |
| **Law changes** (Children's code finalised after 10 Dec 2026; Apple payment orders from 1 Mar 2027) | OAIC and court updates | Re-read Step 5 items 6–7 and Step 6 in March 2027. Update the plan. |

**Done when:** for each risk, you know your trigger and your first action without rereading this table.

---

## Start Monday (30 November 2026): the first 5 actions

1. **Run `/goal` with `docs/goal-prompt.md`** to build v1. Nothing else in this plan moves until it's on your phone.
2. **Apply for an ABN** at [register.business.gov.au](https://register.business.gov.au/). It's free and takes 20 minutes.
3. **Enrol in the Apple Developer Program** so you can use TestFlight. It costs US$99 or the local-currency equivalent (**ASSUMPTION:** about A$149).
4. **Start the outreach spreadsheet:** WDNA rep coordinator, 2 WDNA club presidents, then the DPNA and MDNA secretaries. Record committee meeting nights.
5. **Message the WDNA rep coordinator** to book a 15-minute coffee before 18 December. The ask is 4 SSA and GBNL coaches piloting free from 1 Feb 2027 (script in Step 2).

---

## Self-check against the brief (re-run each time this file changes)

| Step | What to do | Worked example using real v1 features | AUD cost and time | Done check |
|---|---|---|---|---|
| 1 Audit | ✔ | ✔ feature table plus grep audit | ✔ $0, 2 h | ✔ |
| 2 Validate | ✔ | ✔ WDNA SSA/GBNL pilot on real 2027 dates; pilot script | ✔ $0 plus dev fee, 3 h/week | ✔ thresholds |
| 3 Pricing | ✔ | ✔ price card, pricing page, per-team and per-player maths | ✔ $0, 1 h | ✔ |
| 4 Product changes | ✔ | ✔ mapped to spec modules; first-club walkthrough | ✔ backend ASSUMPTION, 3–5 weeks | ✔ two-phone test |
| 5 Legal | ✔ | ✔ fact sheet; ABN, GST, privacy, child safety, fees | ✔ itemised | ✔ |
| 6 Payments | ✔ | ✔ invoice #0001 with fee maths | ✔ $0 setup | ✔ |
| 7 Launch | ✔ | ✔ listing, screenshots, 4 scripts, posts, referral | ✔ itemised | ✔ |
| 8 90 days | ✔ | ✔ week-by-week tied to 2027 calendar dates | ✔ (inside Steps 1–7) | ✔ W13 target |
| 9 Numbers | ✔ | ✔ three cases | ✔ | ✔ monthly update |
| 10 Risks | ✔ | ✔ app-specific triggers | ✔ | ✔ |

Open items to re-verify:
- Every **ASSUMPTION:** line above.
- The Step 1 audit, once real code is pushed.
- The final Children's Online Privacy Code, after 10 Dec 2026.
- The status of the Apple payment orders, in March 2027.
