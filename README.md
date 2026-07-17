<h1 style="font-weight:bold;">Kalshi World Cup — Sport Prediction Markets</h1>

## About the data

Each row of the **price panel** is one *contract* observed at one *timestamp* — i.e. the unit of observation is **[contract × time]**. Snapshots are recorded at a regular cadence (**about every 2 minutes**, see `collected_at_utc`, UTC).

### RESEARCH NOTE (Kalshi series tickers): 
These series tickers were confirmed live from the production catalog (not guessed). Each World Cup match is one EVENT within a series; the series determines the contract type, I selected the two most popular based on the size

```
KALSHI_WC_SERIES = {
    "KXWCGAME": "match_winner",    # "World Cup Game" (per-match moneyline)
    "KXWCTOTAL": "goals_ou",       # "World Cup Total" (goals over/under)
```
Extend if needed:
```
    # "KXWCADVANCE": "advancement",  # "World Cup Advance"
    # "KXWCSCORE": "correct_score",  # "World Cup Correct Score"
    # KXWCSPREAD, KXWCGOAL, KXWCBTTS, KXWCTEAMTOTAL,
    # KXWC1H, KXWC1HTOTAL, KXWC1HSCORE, KXWCMOV, KXMENWORLDCUP (outright).
}
```
### Contract types

| `contract_type` | Kalshi series | Options per match | `yes_price` = probability that… |
|---|---|---|---|
| `match_winner` | `KXWCGAME` | **3 markets** — Team A wins · **Tie** (draw) · Team B wins. After the final whistle one team is the **Winner**, one the **Loser**. | …that outcome occurs (a team wins, or the match is drawn). |
| `goals_ou` | `KXWCTOTAL` | A **total-goals ladder** (lines `1`–`7`). | …the match ends with **at least *N* goals** (*over N − 0.5*). Higher lines → lower price. |

The **outcome is encoded in the ticker suffix**: e.g. `KXWCGAME-…-ARG` (Argentina wins), `…-TIE` (draw), `KXWCTOTAL-…-3` (≥ 3 total goals).

### 🧾 Column dictionary — price panel

| Column | Meaning |
|---|---|
| `collected_at_utc` | Snapshot time in **UTC** (~every 1–2 min) |
| `ticker` | Kalshi market id; **suffix = outcome** (team code, `TIE`, or goals line) |
| `contract_type` | `match_winner` or `goals_ou` |
| `yes_price` | Last traded **YES** price ($0–1) ≈ implied probability |
| `yes_bid` / `yes_ask` | Best **buy / sell** price for the YES side ($) |
| `no_bid` / `no_ask` | Best **buy / sell** price for the NO side ($) |
| `volume` | Contracts traded during the bar |
| `open_interest` | Contracts still outstanding |
| `end_period_ts` | Unix timestamp of the bar's end |
| `match_code` | Match key, e.g. `26JUL15ENGARG` = *15 Jul 2026, ENG vs ARG* |

### 🧾 Column dictionary — `match_features.csv` (one row per match)

| Column | Meaning |
|---|---|
| `home_team` / `away_team` | Team codes (home vs away) |
| `home_score` / `away_score` / `total_goals` | ⚽ Final score |
| `winner` | Winning team code, or `DRAW` |
| `result_type` | `FT` (full time) · `AET` (extra time) · `Pens` (shootout) |
| `went_to_extra_time` / `went_to_penalties` | Boolean flags |
| `minutes_played` | ⏱️ Duration proxy (final game clock) |
| `odds_home_ml` / `odds_draw_ml` / `odds_away_ml` | 🎲 Bookmaker **moneyline** odds |
| `odds_spread` / `odds_total` | Bookmaker spread & total-goals line |
| `attendance` / `venue` | Match context |
| `scorers` | Goal scorers (best-effort; may be empty) |

---