# Netrunner Quick Draft — Implementation Specification

> Extracted from the [jinteki.net source code](https://github.com/mtgred/netrunner) (`src/clj/game/core/quick_draft.clj` and `src/cljc/jinteki/chimera.cljc`).

---

## 1. Overview

Quick Draft is a two-player, simultaneous drafting format for Android: Netrunner. Instead of bringing pre-built decks, both players draft their Corp and Runner decks at the start of the game through a series of pick-one-from-many choices. The resulting decks are smaller than standard constructed decks and the game is played to a lower agenda-point threshold.

The UI description from jinteki.net reads:

> *"Quickly draft a deck to play against your opponent, using a smaller deck size and lower than normal agenda-point total."*

---

## 2. Game Parameters

| Parameter | Corp | Runner |
|---|---|---|
| **Agenda points to win** | **5** | **6** |
| **Points in Corp deck** | 14 | — |
| **Starter (auto-included) cards** | 3× Hedge Fund, 2× Jackson Howard | 3× Blueberry!™ Diesel, 2× Sure Gamble, 1× Crypsis |
| **Final deck size (approx.)** | ~34 cards | ~35 cards |

The asymmetric win condition (Corp wins at 5 points, Runner at 6) is set at game start.

---

## 3. Draft Structure

The draft is **simultaneous** — both players draft their Corp deck and Runner deck in parallel. At each step, both the Corp prompt and the Runner prompt are shown; the game waits until both players have made their pick before advancing.

The draft proceeds in **three phases**:

### Phase 1: Info + Stage One Picks

1. **Info step** — each side is told what starter cards are auto-included in their deck.
2. **Stage One picks** — a sequence of "pick 1 card, receive N copies" choices.

### Phase 2: Identity Selection

3. **Identity pick** — each side chooses 1 identity from 4 randomly offered.

### Phase 3: Stage Two Picks

4. **Stage Two picks** — additional "pick 1, receive N copies" rounds with more choices per round.

The Corp and Runner draft queues are zipped together step-for-step, so the total number of draft steps is the same for both sides.

---

## 4. Corp Draft — Detailed Steps

### 4.1 Starter Cards (auto-included)

| Qty | Card |
|-----|------|
| 3 | Hedge Fund |
| 2 | Jackson Howard |

These 5 cards are added to the deck automatically. The player is shown an info prompt confirming this.

### 4.2 Stage One (7 picks)

Picks are presented in this order:

| # | Pick Type | Copies Received | Choices Shown | Pool |
|---|-----------|-----------------|---------------|------|
| 1 | **3-point Agenda** | **2** copies | 5 random from [valid-3pointers](#51-valid-3-point-agendas) | — |
| 2 | **2-point Agenda** | **4** copies | 5 random from [valid-2pointers](#52-valid-2-point-agendas) | — |
| 3 | **Ice** | **3** copies | 12 random ice (by card title) | All corp ice not on ban list |
| 4 | **Card** | **3** copies | 9 random | All non-agenda, non-identity, non-banned corp cards |
| 5 | **Card** | **3** copies | 9 random | (same pool) |
| 6 | **Card** | **3** copies | 9 random | (same pool) |
| 7 | **Card** | **3** copies | 9 random | (same pool) |

After stage one, the corp has: 5 starters + 2 + 4 + 3 + 3 + 3 + 3 + 3 = **26 cards**.

### 4.3 Identity Pick

4 identities are randomly drawn from the [valid corp identity list](#53-valid-corp-identities). The player picks 1.

### 4.4 Stage Two (4 picks)

| # | Pick Type | Copies Received | Choices Shown | Pool |
|---|-----------|-----------------|---------------|------|
| 1 | **Card** | **2** copies | 12 random | Same non-agenda, non-identity, non-banned pool |
| 2 | **Card** | **2** copies | 12 random | (same pool) |
| 3 | **Card** | **2** copies | 12 random | (same pool) |
| 4 | **Card** | **2** copies | 12 random | (same pool) |

Stage two adds: 2 + 2 + 2 + 2 = **8 cards**.

**Corp total: ~34 cards** (26 from stage one + 8 from stage two).

---

## 5. Runner Draft — Detailed Steps

### 5.1 Starter Cards (auto-included)

| Qty | Card |
|-----|------|
| 3 | Blueberry!™ Diesel |
| 2 | Sure Gamble |
| 1 | Crypsis |

These 6 cards are added automatically.

### 5.2 Stage One (7 picks)

The runner's card pool is split into sub-pools:

- **non-programs**: all runner cards that are NOT Fracters, Decoders, or Killers (but may include other program subtypes)
- **all runner cards**: the full pool of non-banned, non-identity runner cards
- **fracters / decoders / killers**: filtered by subtype

| # | Pick Type | Copies | Choices Shown | Pool |
|---|-----------|--------|---------------|------|
| 1 | **Card** | **3** | 9 random | non-programs only |
| 2 | **Card** | **3** | 10 random | all runner cards |
| 3 | **Card** | **3** | 9 random | non-programs only |
| 4 | **Card** | **3** | 10 random | all runner cards |
| 5 | **Fracter** | **2** | 4 fracters + 3 non-programs (7 total) | mixed |
| 6 | **Decoder** | **2** | 4 decoders + 3 non-programs (7 total) | mixed |
| 7 | **Killer** | **2** | 4 killers + 3 non-programs (7 total) | mixed |

After stage one, the runner has: 6 starters + 3 + 3 + 3 + 3 + 2 + 2 + 2 = **24 cards**.

The breaker picks (5–7) are "multi-picks": the choice pool is a mix of on-type breakers and non-program cards, guaranteeing the player *can* pick a breaker for each ice type but isn't forced to.

### 5.3 Identity Pick

4 identities are randomly drawn from the [valid runner identity list](#54-valid-runner-identities). The player picks 1.

### 5.4 Stage Two (4 picks)

| # | Pick Type | Copies | Choices Shown | Pool |
|---|-----------|--------|---------------|------|
| 1 | **Card** | **2** | 12 random | non-programs only |
| 2 | **Card** | **2** | 12 random | all runner cards |
| 3 | **Card** | **2** | 12 random | non-programs only |
| 4 | **Card** | **2** | 12 random | all runner cards |

Stage two adds: 2 + 2 + 2 + 2 = **8 cards**.

**Runner total: ~32 cards** (24 from stage one + 8 from stage two).

---

## 6. Card Lists

### 6.1 Valid 3-Point Agendas

```
Bellona
City Works Project
Degree Mill
Elective Upgrade
Fuji Asset Retrieval
Global Food Initiative
Ikawah Project
Obokata Protocol
Project Vacheron
SDS Drone Deployment
Send a Message
SSL Endorsement
The Basalt Spire
The Future Perfect
Vulnerability Audit
```

### 6.2 Valid 2-Point Agendas

```
Above the Law
Accelerated Beta Test
AstroScript Pilot Program
Azef Protocol
Broad Daylight
Blood in the Water
Cyberdex Sandbox
Longevity Serum
Medical Breakthrough
NAPD Contract
Freedom of Information
Oaktown Renovation
Offworld Office
Philotic Entanglement
Project Beale
Tomorrow's Headline
```

### 6.3 Valid Corp Identities

```
Strategic Innovations: Future Forward
Synthetic Systems: The World Re-imagined
Information Dynamics: All You Need To Know
Fringe Applications: Tomorrow, Today
Cybernetics Division: Humanity Upgraded
Hyoubu Institute: Absolute Clarity
AgInfusion: New Miracles for a New World
PT Untaian: Life's Building Blocks
NBN: The World is Yours*
Pravdivost Consulting: Political Solutions
Argus Security: Protection Guaranteed
Thule Subsea: Safety Below
Sportsmetal: Go Big or Go Home
Jinteki: Replicating Perfection
NBN: Reality Plus
Epiphany Analytica: Nations Undivided
Weyland Consortium: Built to Last
Haas-Bioroid: Precision Design
The Outfit: Family Owned and Operated
Earth Station: SEA Headquarters
```

### 6.4 Valid Runner Identities

```
Hayley Kaplan: Universal Scholar
Lat: Ethical Freelancer
Jamie "Bzzz" Micken: Techno Savant
Ele "Smoke" Scovak: Cynosure of the Net
Nasir Meidan: Cyber Explorer
Rielle "Kit" Peddler: Transhuman
Captain Padma Isbister: Intrepid Explorer
Nero Severn: Information Broker
Boris "Syfr" Kovac: Crafty Veteran
Barry "Baz" Wong: Tri-Maf Veteran
Silhouette: Stealth Operative
Zahya Sadeghi: Versatile Smuggler
Az McCaffrey: Mechanical Prodigy
Gabriel Santiago: Consummate Professional
Wyvern: Chemically Enhanced
Edward Kim: Humanity's Hammer
Nathaniel "Gnat" Hall: One-of-a-Kind
Topan: Ormas Leader
Sebastião Souza Pessoa: Activist Organizer
Quetzal: Free Spirit
Reina Roja: Freedom Fighter
```

### 6.5 Runner Ban List

These runner cards are excluded from the draft pool entirely:

<details>
<summary>Full runner ban list (click to expand)</summary>

**Events:**
Blackmail, Calling in Favors, Charm Offensive, Data Breach, Diana's Hunt, Direct Access, Employee Strike, Exploratory Romp, Feint, Government Investigations, Immolation Script, Itinerant Protesters, Leverage, Mass Install, Networking, Office Supplies, Paper Tripping, Populist Rally, Power Nap, Rebirth, Reboot, Spree, Surge, Traffic Jam, Uninstall

**Hardware:**
Acacia, Archives Interface, BMI Buffer, Bookmark, Capstone, Capybara, Deep Red, Ekomind, Forger, LLDS Processor, MemStrips, Monolith, Mu Safecracker, Muresh Bodysuit, Plascrete Carapace, Public Terminal, Qianju PT, Rabbit Hole, Ramujan-reliant 550 BMI, Recon Drone, Replicator, Security Chip, Titanium Ribs, Unregistered S&W '35, Window

**Resources:**
Activist Support, Adjusted Chronotype, Akshara Sareen, Angel Arena, Assimilator, Bazaar, Bio-Modeled Network, Chrome Parlor, Citadel Sanctuary, Cookbook, Crash Space, Debbie "Downtown" Moreira, District 99, Donut Taganes, Dr. Lovegood, Fester, Bloo Moose, Rezeki, First Responders, Gene Conditioning Shoppe, Globalsec Security Clearance, Investigative Journalism, Investigator Inez Delgado (all versions), Jarogniew Mercs, Keros Mcintyre, Liberated Chela, Motivation, New Angeles City Hall, Off-Campus Apartment, Order of Sol, Paige Piper, Paparazzi, Power Tap, Public Sympathy, Sacrificial Clone, Shadow Team, Starlight Crusade Funding, Synthetic Blood, Tallie Perrault, The Back, Thunder Art Gallery, Underworld Contact, Urban Art Vernissage, Valentina Ferreira Carvalho, Virus Breeding Ground, Wasteland, Whistleblower, Wireless Net Pavilion

**Icebreakers:**
Crowbar, Dai V, Shiv, Spike

**Programs:**
Au Revoir, Copycat, Disrupter, Flux Capacitor, Heliamphora, Hivemind, Incubator, Ixodidae, LLDS Energy Regulator, Panchatantra, Pawn, Plague, Progenitor, Surveillance Network Key (all versions)

</details>

### 6.6 Corp Ban List

These corp cards are excluded from the general draft pool (agendas and identities have their own curated lists):

<details>
<summary>Full corp ban list (click to expand)</summary>

Ad Blitz, Aimor, Ancestral Imager, Anson Rose, Aryabhata Tech, Awakening Center, Back Channels, Backroom Machinations, Bad Times, Bamboo Dome, Big Brother, Bioroid Efficiency Research, Bioroid Work Crew, Blacklist, Bloom, Bloodletter, Bloop, Brain-Taping Warehouse, Broadcast Square, Builder, Bullfrog, Burke Bugs, Business As Usual, Cerebral Static, Cell Portal, Chairman Hiro, ChiLo City Grid, CFC Excavation Contract, Chronos Project, Clyde Van Rite, Cold Site Server, Commercialization, Constellation Protocol, Corporate War, Cyberdex Trial, Dedicated Response Team, Dedication Ceremony, Defective Brainchips, Director Haas, Drone Screen, Early Premiere, Encrypted Portals, Enforced Curfew, Executive Retreat, Fast Break, Firmware Updates, Foxfire, Freelancer, Fumiko Yamamori, Fractal Threat Matrix, Freedom of Information, Gemini, Henry Phillips, Hasty Relocation, Helium-3 Deposit, Hellion Alpha Test, Hollywood Renovation, Howler, Hudson 1.0, Hyoubu Research Facility, Illicit Sales, Improved Protein Source, Improved Tracers, Increased Drop Rates, Ireress, Investigator Inez Delgado A (all versions), Lakshmi Smartfabrics, Lt. Todachine 2, Lockdown, Macrophage, Mark Yale, MCA Informant, Medical Breakthrough, Megaprix Qualifier, Merlin, Metamorph, Merger, Midnight-3 Arcology, Midori, Minelayer, Mumbad Construction Co., Net Analytics, Net Police, Net Quarantine, Net Watchlist, New Construction, NEXT Wave 2, Observe and Destroy, Otoroshi, PAD Factory, Paper Trail, Pachinko, Personality Profiles, Primary Transmission Dish, Priority Construction, Product Recall, Project Ares, Project Wotan, Psychographics, Puppet Master, Ravana 1.0, Rebranding Team, Reconstruction Contract, Regulatory Capture, Remote Data Farm, Research Grant, Restoring Face, Rutherford Grid, Sacrifice, Salvage, Salem's Hospitality, Satellite Grid, Sealed Vault, Searchlight, Self-Destruct Chips, Sentinel Defense Program, Shipment from Kaguya, Shoot the Moon, Signal Jamming, Simone Diego, Snatch and Grab, Space Camp, Storgotic Resonator, Student Loans, Subcontract, Success, Sunset, Superior Cyberwalls, Synth DNA Modification, Test Ground, The Board, The News Now Hour, The Twins, Tori Hanzō, Traffic Accident, Trieste Model Bioroids, Trojan, Tyr's Hand, Tyrant, Underway Grid, Underway Renovation, Upayoga, Vulcan Coverup, Victoria Jenkins, Warden Fatuma, Watchdog, Wendigo, Wetwork Refit, Witness Tampering, Woodcutter, Zed 1.0

</details>

---

## 7. Drafting Algorithm — Pseudocode

```
function generateQuickDraft():
    corp  = generateCorpDraft()
    runner = generateRunnerDraft()

    draftQueue = [
        { type: INFO,     corp: corp.info,     runner: runner.info     },
        ...zip(corp.stageOne, runner.stageOne),   // 7 paired steps
        { type: IDENTITY, corp: corp.identity, runner: runner.identity },
        ...zip(corp.stageTwo, runner.stageTwo),   // 4 paired steps
    ]

    return draftQueue   // 1 + 7 + 1 + 4 = 13 steps total


function generateCorpDraft():
    pool = allCorpCards
        .exclude(identities)
        .exclude(agendas)
        .exclude(corpBanList)
        .exclude(set == "tdc")       // exclude old draft-only cards

    icePool = pool.filter(isIce)

    return {
        info: { autoCards: [3× "Hedge Fund", 2× "Jackson Howard"] },
        stageOne: [
            pick(valid3Pointers, show=5, copies=2),     // 3pt agenda
            pick(valid2Pointers, show=5, copies=4),     // 2pt agenda
            pick(shuffle(icePool).take(12), copies=3),  // ice
            pick(shuffle(pool).take(9),  copies=3),     // card
            pick(shuffle(pool).take(9),  copies=3),     // card
            pick(shuffle(pool).take(9),  copies=3),     // card
            pick(shuffle(pool).take(9),  copies=3),     // card
        ],
        identity: pick(shuffle(validCorpIDs).take(4), copies=1),
        stageTwo: [
            pick(shuffle(pool).take(12), copies=2),     // card
            pick(shuffle(pool).take(12), copies=2),     // card
            pick(shuffle(pool).take(12), copies=2),     // card
            pick(shuffle(pool).take(12), copies=2),     // card
        ]
    }


function generateRunnerDraft():
    pool = allRunnerCards
        .exclude(identities)
        .exclude(runnerBanList)
        .exclude(set == "tdc")

    nonPrograms = pool.filter(NOT hasSubtype("Fracter"|"Decoder"|"Killer"))
    fracters    = pool.filter(hasSubtype("Fracter"))
    decoders    = pool.filter(hasSubtype("Decoder"))
    killers     = pool.filter(hasSubtype("Killer"))

    return {
        info: { autoCards: [3× "Blueberry!™ Diesel", 2× "Sure Gamble", 1× "Crypsis"] },
        stageOne: [
            pick(shuffle(nonPrograms).take(9),  copies=3),   // non-breaker
            pick(shuffle(pool).take(10),        copies=3),   // any card
            pick(shuffle(nonPrograms).take(9),  copies=3),   // non-breaker
            pick(shuffle(pool).take(10),        copies=3),   // any card
            multiPick(fracters, 4, nonPrograms, 3, copies=2),// fracter pick
            multiPick(decoders, 4, nonPrograms, 3, copies=2),// decoder pick
            multiPick(killers,  4, nonPrograms, 3, copies=2),// killer pick
        ],
        identity: pick(shuffle(validRunnerIDs).take(4), copies=1),
        stageTwo: [
            pick(shuffle(nonPrograms).take(12), copies=2),   // non-breaker
            pick(shuffle(pool).take(12),        copies=2),   // any card
            pick(shuffle(nonPrograms).take(12), copies=2),   // non-breaker
            pick(shuffle(pool).take(12),        copies=2),   // any card
        ]
    }
```

---

## 8. Key Design Notes for Implementers

### 8.1 Simultaneous Drafting
Both players see their Corp pick and Runner pick at the same time. The game does *not* advance until both players have submitted their choices. If one player finishes first, they see a "waiting for opponent" message.

### 8.2 Copy Counts
When a player picks a card, they receive multiple copies (the `qty` value). Stage One generally gives 3 copies per pick (or 2–4 for agendas); Stage Two gives 2 copies per pick. This is how decks reach ~34 cards from relatively few choices.

### 8.3 Shuffle-per-pick
Each pick step independently shuffles the relevant pool and takes the top N. This means a card that appeared in one pick's choices may or may not appear in another pick. Duplicates across pick steps are possible (and fine — the player just gets more copies).

### 8.4 Breaker Guarantee
The Runner's stage-one picks 5–7 use a "multi-pick" that mixes breakers of the needed type with non-program cards. This means the runner is always *offered* at least one Fracter, one Decoder, and one Killer during the draft, but can choose a non-breaker instead if they're feeling bold (they do start with Crypsis as backup).

### 8.5 Card Data Source
The original implementation pulls card data from [NetrunnerDB](https://netrunnerdb.com). Cards with set code `"tdc"` (The Draft Collection — old FFG draft-only cards) are excluded from the pool.

### 8.6 After the Draft
Once all picks are made, both Corp and Runner decks are shuffled. The `:draft` state is removed and normal Netrunner gameplay begins with the modified win conditions (Corp → 5 pts, Runner → 6 pts).

### 8.7 No Influence Restriction
Since each card is drafted independently without faction constraints, and the identity is chosen separately after most cards are picked, there are no influence limits in Quick Draft.

---

## 9. Standalone Web Tool — Suggested Architecture

For a standalone drafting webpage (not connected to jinteki.net gameplay), you would need:

1. **Card database** — fetch from the [NetrunnerDB API](https://netrunnerdb.com/api/2.0/doc) or bundle a JSON snapshot. You need: title, side (Corp/Runner), type (Identity, Agenda, Ice, etc.), subtypes, agenda points, set code.

2. **Ban list filtering** — apply the runner and corp ban lists above before generating any pick pools.

3. **Draft state machine** — walk through the 13-step queue, presenting picks in order. Track: current step, cards selected so far, deck contents for each side.

4. **Output** — at the end, display the complete Corp deck list and Runner deck list (with copy counts). Optionally, allow export as plain text compatible with jinteki.net's deck builder paste format.

### Minimal Data Model

```typescript
interface DraftPick {
    type: "info" | "deck" | "identity";
    side: "corp" | "runner";
    prompt: string;
    choices: string[];          // card titles
    copiesReceived: number;     // how many copies the player gets
}

interface DraftState {
    currentStep: number;        // 0..12
    queue: DraftStep[];         // 13 steps, each has a corp + runner pick
    corpDeck: Map<string, number>;     // title → count
    runnerDeck: Map<string, number>;
    corpIdentity: string | null;
    runnerIdentity: string | null;
}
```

---

## 10. Source References

- **Quick Draft logic**: [`src/clj/game/core/quick_draft.clj`](https://github.com/mtgred/netrunner/blob/master/src/clj/game/core/quick_draft.clj)
- **Corp ban list**: [`src/cljc/jinteki/chimera.cljc`](https://github.com/mtgred/netrunner/blob/master/src/cljc/jinteki/chimera.cljc) → `corp-bans`
- **Runner ban list (quick draft variant)**: `quick_draft.clj` → `runner-bans` (slightly different from chimera's list)
- **Game setup integration**: [`src/clj/game/core/set_up.clj`](https://github.com/mtgred/netrunner/blob/master/src/clj/game/core/set_up.clj)
- **UI format selector**: [`src/cljs/nr/new_game.cljs`](https://github.com/mtgred/netrunner/blob/master/src/cljs/nr/new_game.cljs)
