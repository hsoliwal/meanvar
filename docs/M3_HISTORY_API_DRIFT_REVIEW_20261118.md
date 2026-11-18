# M3: visible Git-history and public API-change review — meanvar

**Status: REVIEW_ONLY / PARTIAL**, observed 2026-11-18T12:38:38.779Z. This is a read-only
history report, not an automatic declaration of a bad commit or permission
to revert upstream's intentional changes. No production source is changed.

## Repository identities and actual coverage

- Repository: `hsoliwal/meanvar`; declared default branch: `master`.
- Pinned head: `b658526f81826e39c31823d583ebe6f6d6d9fe45`; current commit contains only the additive `.github/MERGE_RULE.md` policy file.
- Earliest commit on the **currently advertised** `master` ancestry:
  `37e53c814719a1bed5df5c263d3bb57211901934` (2015-10-06).
- GitHub commit-list pages: `per_page=100&page=1` returned **35**; page 2 returned **zero**.
  All 35 commit parent identities form one complete single-parent chain ending at the root,
  with no merge commit in that chain.
- Exact GitHub `git/refs` response advertised **one ref**, `refs/heads/master` at the
  pinned head. Branch search page 1 returned one name and its final cursor page returned zero.
- GitHub pull-request list `state=all`, pages 1 and 2: **zero results**.
- Every one of the 35 commit detail endpoints was read and its changed-file list
  inspected; the current Git tree was fetched with `recursive=1` and
  `truncated=false` (**34 entries including tree nodes**).
- License: root `LICENSE` Git blob `d8e162d6d0841614b91119e366ce2dc1aa7b7a09`,
  **MIT**, preserved unchanged.

**Coverage fence:** the observation covers currently API-advertised refs and their
35 listed commits, not dangling/unreachable or deleted refs, reflogs, GitHub-inaccessible
objects, LFS data or absent remote namespaces. The full bare-mirror
`git log --all --root --full-history -m` historical-version scanner and
compiler/runtime compatibility proofs were **NOT_RUN**. Do not mark the
canonical complete-repository history gate VERIFIED.

## Specific public API loss/change candidates

Commit `043a76bd1066e849076da3c005f1157e753ebafd`
(2015-10-07) explicitly replaced the historical simple calculation methods.
Exact preimage source at its parent `68508397fec212e7d2625b7f4f054f2bc414ca49`:
`MeanVarianceSlidingWindow.java` Git blob
`320246291458a5b187ac7cf3b98bb72523d19dfe`.
The modern file at pinned `master`: `8ea146af91118f0d5d297745c17b5baab54810b3`.

Public no-argument methods present before that commit but absent from current
`MeanVarianceSlidingWindow`:
- `calculateMeanSimple()`
- `calculateVarianceSimple()`
- `calculateVarianceSimpleBiased()`

Commit `def6e8e1684bc80b98537a916495b1997fe3b54b`
(2015-12-29) renamed biased/unbiased methods in both public classes.
The earlier `MeanVarianceSampler.java` blob was
`c774f6ee1e0889bc77ac5ac5aa3c36ebe19285ff`;
its immediate postimage is
`8b72df62b44b87254c4e7ea191aa31817f1b408a`.
Current `MeanVarianceSampler.java` blob:
`0d1c347cc89e3d4d55e54f74de4790d27daa7ee6`.

The earlier `getVarianceBiased()` and `getStdDevBiased()` public method
names are absent in both current classes; current explicit unbiased methods
exist instead. More importantly, in the prior sampler `getVariance()` used
`s/(count-1)` for positive counts (unbiased), while the current
`getVariance()` uses `s/count` (biased). The original unbiased operation
is now named `getVarianceUnbiased()`. Similarly, prior `getStdDev()` was
unbiased while current `getStdDev()` uses the biased variance.

For two values 1 and 3 the expected sample variance is 2 and the population
variance is 1: the same historical `getVariance()` signature can therefore
return a different numerical result. This is **source-proven potential semantic
API drift**, not an executed differential runtime test or a licensing verdict.

## Admissible resolution, not automatic restoration

Keep the current numerically stable sampler and all present APIs. Before any
compatibility change, determine which historical upstream versions the fork
claims to support, measure rounding/single-element/empty-window behavior,
and choose a versioned adapter/migration where one signature cannot represent
both historical and current meanings. Older simple methods may be proposed
as additive, explicitly named operations only after a tested Maven/OpenRewrite
recipe and source/binary/behavior proof. Retain the old MIT attribution;
do not relicense upstream to Apache-2.0 and do not use an LLM as a license
laundering mechanism. No algorithm/performance winner is declared here.

## Next gates and last checked

- Candidate disposition: `HOLD_API_CONTRACT_REVIEW` for the above method changes.
- Read-only GitHub metadata/commit check: 2026-11-18T12:38:38.779Z.
- Last *complete mirror-based* historical review: **NOT_RUN**.
- Last *API/ABI/compiler/runtime* verification: **NOT_RUN**.
- Last accepted production-compatible revision: **NOT_ESTABLISHED**.
- Previous verified full-graph checkpoint: **NONE_OBSERVED**.

Run complete source-ref/custody reconciliation, API compatibility tests and
versioned Synexia recipe evidence before proposing any source modification.
