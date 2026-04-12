# LocaLiquid — Feature Roadmap

## Status: 3 high-value features planned

---

## LQ-F1: Milestone Achievement Overlay

**Summary:** When a user logs a snapshot that crosses a net worth milestone for the first time, show a full-screen celebratory overlay with an animated ring and the milestone amount, instead of just a toast.

**Value:** Milestones are the emotional payoff of tracking finances. A toast disappears in 3 seconds. A celebration overlay creates a moment worth sharing.

**Milestone thresholds:** $10k, $25k, $50k, $100k, $250k, $500k, $1M

**Trigger logic:**
- On `logSnapshot()`, compare new net worth vs all prior snapshots
- If new NW crosses a threshold that no prior snapshot has crossed → show overlay
- Mark hit milestones in `locaLiquidData.milestonesHit = [10000, 50000, ...]` to prevent re-triggering

**Overlay UI:**
- Full-screen fixed overlay (`z-index: 9998`) with `rgba(0,0,0,0.85)` background
- Center card with animated expanding ring (CSS keyframe, cyan stroke)
- Milestone amount in large bold Space Mono (cyan)
- Subtitle: "Net Worth Milestone Reached"
- "Celebrate 🎉" dismiss button (or tap anywhere to dismiss)
- Auto-dismisses after 6 seconds

**TODO:**
- [ ] Add CSS keyframe `@keyframes ring-expand` for the SVG ring animation
- [ ] Add `showMilestoneOverlay(amount)` function
- [ ] Add `<div id="milestoneOverlay" class="hidden">` to the HTML body
- [ ] Update `logSnapshot()` to check for first-time milestone crossing
- [ ] Persist `milestonesHit` array in `locaLiquidData`
- [ ] Add `data-testid="milestone-overlay"` and `data-testid="milestone-amount"` for tests

---

## LQ-F2: Inflation-Adjusted Projection Toggle

**Summary:** A toggle in the Projection tab that switches the projected net worth table between nominal (raw) dollars and inflation-adjusted (real) dollars, using a configurable inflation rate (default 3%).

**Value:** "$1.2M by age 65" sounds great until you realize it's worth $540k in today's dollars. This is a premium feature that makes the projection honest.

**Calculation:**
- Real value at year Y = nominal / (1 + inflationRate)^Y
- Inflation rate input added to the projection settings form (default 3%)

**UI:**
- Toggle button "Nominal / Real $" above the projection table (like the strategy buttons in LocaLoan)
- When "Real $" is active, all projected values deflate using the stored inflation rate
- Table header updates: "Projected Net Worth (Real $, {inflationRate}% inflation)"
- Readiness gauge also updates to use real retirement-date value

**TODO:**
- [ ] Add `_showRealDollars = false` state variable
- [ ] Add `inflationRate` field to projection settings (default 3.0)
- [ ] Add `deflate(nominal, years, rate)` helper: `nominal / Math.pow(1 + rate/100, years)`
- [ ] Add nominal/real toggle above the projection table
- [ ] Re-render projection table on toggle without full page re-render
- [ ] Persist `inflationRate` in `locaLiquidData.projections`
- [ ] Add `data-testid="real-toggle"` and `data-testid="proj-table"` for tests

---

## LQ-F3: Snapshot Comparison View

**Summary:** In the Snapshot tab history list, each snapshot row gets a "Compare" button. Tapping it selects that snapshot as "A". Tapping another selects it as "B". A diff card then appears at the top showing the delta in assets, liabilities, net worth, and each data source.

**Value:** "How much did my net worth grow in the last 6 months?" is the most common question users have. Right now they have to subtract manually. This gives them a one-tap answer with a full breakdown.

**UI:**
- Each snapshot row gets a small "Compare" chip button on the right
- First tap: snapshot A is highlighted in cyan border
- Second tap on a different snapshot: diff card appears at top of the Snapshot tab
- Diff card shows: period (date A → date B), net worth change (+/−), asset delta, liability delta
- "Clear" button resets the comparison

**State:** `_compareA` and `_compareB` are module-level variables (not persisted).

**TODO:**
- [ ] Add `_compareA = null` and `_compareB = null` state variables
- [ ] Add `selectCompare(snapshotDate)` function with A/B toggle logic
- [ ] Add "Compare" button chip to each snapshot row in `renderSnapshot()`
- [ ] Add `renderDiffCard(snapA, snapB)` returning HTML for the diff card
- [ ] Insert diff card at top of snapshot list when both A and B are set
- [ ] Add `data-testid="compare-btn-${date}"`, `data-testid="diff-card"` for tests
