# MILO full-product iOS parity validation — 2026-09-26

Specdrive was applied to MILO's full iOS product, using the current HarmonyOS
product as the intended reference. This extends the earlier offline diary slice;
the [2026-09-25 pilot](validation-2026-09-25.md) does not establish full product
parity. This report records three completed visual-repair journey rounds and
the subsequent failed full-state capture. Product acceptance remains open.

## Scope and evidence

The OpenSpec change `ios-harmony-product-parity` requires 13 principal pages and
53 states, including eight at the largest accessibility text size. Final
acceptance requires actual HarmonyOS and iOS screenshots of matching states,
working interactions, readable content and specific aesthetic improvements.

The local environment now has full Xcode 26.6 and an iOS 26.5 runtime. Native
checks use an **iPhone 17e simulator**, not a physical iPhone. The earlier pilot's
missing-local-Xcode limitation is historical; the remaining product acceptance
gaps are listed below.

- 31 core tests, 26 coordinator checks, 24 evidence-validator tests and 19
  framework tests passed.
- The initial full-product phase passed nine native interaction tests and
  captured all 53 states, producing **183 original images**.
- Independent reviewers actually inspected 97 attachments after deduplicating
  within each state; these contained 91 globally distinct image byte sequences.
  The review found template-layout defects, large-text readability problems,
  an error fixture without its error message and gaps in long-page coverage.

Those findings remained failures even though the interaction tests passed.
The images were candidates for product review, not an automatic approval.

## Visual-repair results at this report's cutoff

| Repair round | Native result | Recorded outcome |
| --- | --- | --- |
| 1 | 5/11 passed; 6 failed | Accessibility identifiers and input targeting exposed failures across several journeys. |
| 2 | 9/11 passed; 2 failed | Template geometry and visible login-error assertions passed. Long-text tail input and the past-memory/template journey still failed. |
| 3 | 11/11 passed; 0 failed | All native journeys passed, including long-text tail input, restart persistence and the past-memory/template journey. This is not a full visual acceptance result. |

The completed repair run IDs are
`journeys-2026-09-26T07-39-44-607Z`,
`journeys-2026-09-26T07-49-40-668Z` and
`journeys-2026-09-26T08-02-03-620Z`. The second round also passed native sharing
for both card templates, saving with the keyboard visible, present-memory
restart persistence and write-failure recovery. The third round closes the two
remaining journey-test failures; visual acceptance still requires separate
evidence and review.

The next full 53-state capture,
`capture-2026-09-26T08-06-55-690Z`, failed on the first state,
`login--email`: the test could not find its measured scroll-geometry probe.
It stopped before producing any named-case capture. The original 183 images
remain the initial build's evidence; they cannot stand in for a complete capture
of the repaired build. Journey attachments are useful for targeted review but
do not replace the missing 53-state matrix.

Independent inspection of the third round's six Collection attachments still
found a P2 visual defect in the actual template-switch journey: the selected
option touches the page counter, and the other option's subtitle is obscured by
the footer. The two dedicated template-layout fixtures look aligned. The image
does not establish whether the difference persists after layout settles; it
remains an open finding, not a visual pass. Two keyboard attachments and the
login-error attachment were reviewed separately, completing inspection of all
nine journey images.

The retained evidence distinguishes failure causes. In the second round, the
long-text test reported a non-hittable TextView, and its diagnostic accessibility
element shared the editor's frame. That proves the failed hit test and overlap;
it does not by itself prove every cause of touch obstruction. The template test
clicked at `(283, 816.667)` in a `390 × 844` window while the template was under
the fixed bottom area. The image and stored fixture still showed the original
template. This was a failed selection, not evidence that a successful selection
was subsequently lost on restart.

## What this taught the framework workflow

1. **Green commands are narrower than product acceptance.** Build and behavior
   tests missed defects found by inspecting actual screenshots. Each required
   state needs visible state assertions and independent image review.
2. **Diagnostics and tests need their own review.** Adding an accessibility
   probe can change how a real control is exposed to XCTest. An `isHittable`
   result alone did not guarantee that the chosen tap point reached the visible
   template. Check the visible geometry and the immediate result of the action,
   while retaining the original persistence assertion.
3. **Long pages require measured coverage.** A fixed number of large swipes
   skipped content. Capture needs actual offsets, viewport bounds, overlapping
   images and a confirmed end. A nested text editor has a separate scroll
   region; its coverage must not be presented as coverage of the whole page.
   The current evidence validator supports one scroll region, so multi-region
   evidence still needs an explicit format and independent validation before
   final acceptance.
4. **Bounded execution has a practical cost.** Every check has at most three
   executions, and successful executions also count. Source changes can make
   earlier evidence stale and require another execution. Review repairs and
   test instrumentation together before spending another native run. Preserve
   incomplete outcomes at the limit; do not change the runner, skill or limits,
   reset state, or start an equivalent run merely to erase failures.

The current 53-state collector is one fail-fast test. Its first instrumentation
failure prevented collection of later states. A future revision should isolate
state failures while keeping the full matrix mandatory, so one missing probe
does not prevent useful evidence from the other states. This is a proposed
improvement, not a change to the executed collector or a relaxed acceptance rule.

The visual-repair phase has now used all three native build executions. This
leaves a real collection failure at the execution boundary despite 11/11
journeys passing. It is a practical cost and an unresolved outcome, not a reason
to weaken the rules or claim that the missing evidence is complete.

The original phase remains in `apps/ios/.specdrive/parity-20260926`. The later
`apps/ios/.specdrive/parity-visual-repair-20260926` phase was supported by
specific failed images, a revised repair plan and independent review. It did
not clear the original record or increase that run's limit. A separately
reviewed scope is not permission to recycle a failed phase until it is green.

## Completion remains open

All **53 paired cases remain pending**. There are no same-state HarmonyOS
screenshots for comparison, no completed physical-iPhone validation, and no
completed real iOS AGC provider/configuration or external-service verification.
The HarmonyOS emulator's initial license confirmation remains outstanding.
The OpenSpec change stays active and the product pull request stays draft;
there is no archive, release or claim of perfect reproduction or improved
aesthetics over HarmonyOS.

This experiment demonstrates coordination, traceable failures and explicit
acceptance gaps. It did not compare elapsed development time or model spending
against other frameworks. Specdrive organizes specifications, work and evidence;
it does not automatically accept a product or replace actual devices, external
services and human-readable product review.

## Evidence locations

This report is derived from these files in the MILO validation working tree,
`/Users/hut/Projects/milo-ios-validation`:

- `docs/operations/specdrive-ios-parity-2026-09-26.md`
- `docs/visual-parity/visual-repair-progress.json`

The progress file records failed test names, run IDs, original attachment paths
and the still-pending paired-review ledger. Original images, `.xcresult` bundles
and native logs live under `apps/ios/evidence/parity/` on the validation machine
and are not committed to Git. The first candidate-image report is
`apps/ios/evidence/parity/iteration-1-report.html`. A clone of Specdrive or MILO
does not contain those original artifacts merely because this document names
them. The third-round journey log and subsequent capture log named above record
the completed 11/11 result and the first-state capture failure respectively.
