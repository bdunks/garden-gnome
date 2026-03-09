# Garden Gnome - Cookie Clicker Garden Automator

## Overview

Garden Gnome automates the [Cookie Clicker](https://orteil.dashnet.org/cookieclicker/) Garden minigame. It strategically plants and harvests crops to unlock all garden seeds and associated upgrades efficiently. The mod runs automatically after each garden tick with no UI or configuration.

**The mod takes full control of the garden.** Any plant not matching the current strategy will be removed. Do not plant manually while the mod is active.

## Installation

### Bookmarklet

Copy this code and save it as a bookmark. Paste it in the URL section. Click the bookmark when the game is open to activate.

```javascript
javascript: (function () {
  Game.LoadMod("https://bdunks.github.io/garden-gnome/gardenGnome.js");
})();
```

### Userscript

For automatic loading, use a [userscript](https://en.wikipedia.org/wiki/Userscript) manager (Tampermonkey or Greasemonkey). Install `gardenGnome.user.js` from this repository. In Tampermonkey, navigate to the file in the GitHub file list, click "Raw", and it should offer to install.

## How It Works

- **Automated Targeting:** Identifies the next seed or upgrade to pursue based on viable options and predefined priorities.
- **Strategic Planting:** Uses layouts optimized for single-parent and multi-parent mutations, following the wiki's [Mutation Setups](https://cookieclicker.fandom.com/wiki/Garden#Mutation_Setups). Layouts adjust dynamically to garden level.
- **Synchronized Growth:** Synchronizes planting times so parent plants mature together, maximizing mutation windows.
- **Soil Optimization:** Switches between Fertilizer (faster growth) and Wood Chips (better mutation odds) based on plant maturity.
- **Auto Sacrifice:** Sacrifices the garden after all seeds and upgrades are unlocked for 10 sugar lumps, then continues the cycle.

## Expected Behaviors

These are things that may look wrong but are working as intended.

**The mod owns the garden.** Any plant not matching the current strategy will be removed. Do not plant manually while the mod is running.

**The entire plot fills with one plant.** Attempting two scenarios cause this:
- **Crumbspore / Brown Mold:** The mod fills the garden with Meddleweed to maximize the probability of a mutation event.
- **Garden upgrade drops:** To maximize cookie drop probability, the mod fills all plot tiles with the required parent plant. This affects all 7 garden upgrades:
  - Green yeast digestives → Green Rot
  - Ichor syrup → Ichorpuff
  - Duketater cookies → Duketater
  - Elderwort biscuits → Elderwort
  - Bakeberry cookies → Bakeberry
  - Wheat slims → Baker's Wheat
  - Fern tea → Drowsy Fern

**Wood Chips on an empty plot.** The mod briefly switches to Wood Chips when the plot is empty (treating it as "mature"). It reverts to Fertilizer once planting begins.  This is a borderline bug.  However, I've made the call that the small amount of time saved isn't worth the increased code complexity, future maintenance burden, and risk of introducing bugs.

**No planting during short CPS buffs.** The mod defers planting during Frenzy, Click Frenzy, building buffs, and dragon buffs to avoid inflated seed costs. Loan buffs (Loan 1/2/3) are exempt from this deferral — they last days, so waiting them out is not practical.

## Known Limitations

- **No UI/Config:** No UI integration, alerts, or configuration options.
- **Two-Parent Mutation Restart:** Waits for full plot decay before restarting two-parent mutations. Single-parent mutations are optimized to detect dead-end states each tick, but two-parent mutations are not.
- **No State Persistence:** The current target is remembered in-memory but cleared on page refresh.  This shouldn't matter because the target selection is deterministic and the mod is stable.
- **No Cookie Balance Check:** Continues planting attempts regardless of cookie balance, which may result in incomplete layouts if short on cookies.  You should enable this mod when you're running with a healthy balance.

## Performance

Performance measured using the [Garden Gnome Runner](https://github.com/bdunks/garden-gnome-runner), which simulates garden minigame ticks in a loop. Statistics based on 1,000 runs from garden reset to full seed log unlock on a Level 9+ plot (6x6), representing approximately 16 simulated years:

| Statistic  | Time to Unlock |
| :--------- | :------------- |
| **Mean**   | 5 days, 17 hrs |
| **Median** | 5 days, 1 hr   |
| **Min**    | 2 days, 23 hrs |
| **Max**    | 17 days, 5 hrs |

| ![Image of a Histogram showing the distribution of runtimes for 1000 Runs](unlock-histogram.png) |
| :----------------------------------------------------------------------------------------------: |
|                                   _Histogram of 1000 runtimes_                                   |

Runtime variability depends nearly entirely on Juicy Queenbeet unlock probability.

**Level 3 gardens:** A 3x3 plot with only Fertilizer available (fewer than 300 Farm buildings) typically takes 3–6 weeks. With Wood Chips unlocked, 2–3 weeks is typical.

The Wiki's [Grinding Sugar Lumps](https://cookieclicker.fandom.com/wiki/Garden#Grinding_Sugar_lumps) strategy suggests completion in around 5.5 days using a different sequence and more nuanced approach. This mod always follows the "Common Setups," and the sequencing is different, but achieves comparable unlock velocity.

## Status & Contributions

Garden Gnome is **feature complete** for its main goal: unlocking all seeds and upgrades. Feature PRs are welcome. Bug reports will be reviewed, but this is a hobby project with limited maintenance time — please be patient.

## Development

To run and test locally:

- Run `npm run dev`
- Add the dev userscript (`./userscripts/gardenGnome.user.dev.js`) to your userscript manager (e.g., Tampermonkey).
  - Test harness assumes a locally hosted [Garden Gnome Runner Tool](https://github.com/bdunks/garden-gnome-runner/) on port `5173` and this mod on port `8080`.
- Refresh Cookie Clicker (or Garden Gnome Runner) after making changes.

The `npm run dev` command runs three processes concurrently: TypeScript compiler in watch mode, Vite bundler in watch mode, and a local server on port 8080 serving `dist/`. Vite does not directly serve the built file in dev mode, hence the custom setup.
