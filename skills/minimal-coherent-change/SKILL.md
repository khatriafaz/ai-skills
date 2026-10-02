---
name: minimal-coherent-change
description: Implement software fixes and features with the smallest coherent change to the existing system. Use when planning or implementing changes in an existing codebase, especially when the user asks to simplify, avoid overengineering, narrow scope, reuse existing flows, or justify changes across layers or repositories. For diagnosis or review requests, apply the reasoning without implementing changes unless authorized.
---

# Minimal Coherent Change

Solve the requested behavior with the least new complexity that correctly satisfies the requirement. Minimal means a coherent solution, not the fewest lines or files. A small patch that misuses existing state or skips required contract changes is not minimal in the useful sense.

## Establish the current baseline

- Inspect the current code, relevant repository instructions, and working-tree changes before deciding what is missing. Distinguish existing capabilities from assumed gaps.
- When the user updates branches or corrects an assumption, re-read the affected paths and reassess the proposal. Do not carry a stale diagnosis into implementation.
- Trace the relevant path from input through interpretation, state transition, action selection, and persistence or output. Inspect only enough surrounding code to locate the missing behavior and its boundaries.
- Separate observed evidence, confirmed code behavior, and hypotheses. Do not claim a cause from a symptom alone.

## Define the smallest complete solution

Before a non-trivial implementation, briefly state:

- The requested behavioral outcome and the missing capability.
- Which existing layer owns that capability and which existing flows can be reused.
- The necessary change surface and what will remain out of scope.

For routine edits, keep this proportionate rather than creating a formal design document. Continue within existing authorization; ask only when an unresolved choice materially changes behavior or scope.

## Preserve ownership and semantics

- Fix behavior at the layer that owns it. Avoid duplicating interpretation, policy, state derivation, or execution logic in another layer merely to force an outcome.
- Preserve distinctions between facts, preferences, completion, intent, eligibility, and execution. Do not fabricate one signal to trigger a downstream flow intended for another.
- Prefer existing contracts and transition machinery. When they cannot express a genuinely distinct concept, add the smallest explicit signal or contract extension rather than overloading an unrelated field.
- In AI-assisted systems, preserve the established division between model interpretation and deterministic validation, persistence, and execution. Prefer improving instructions and supplying correct state over adding a parallel parser or action override, unless a concrete reliability or safety requirement warrants deterministic enforcement.
- Reuse existing action selection and eligibility paths. An override, retry loop, fallback, helper, abstraction, migration, or new state field needs a demonstrated requirement, not a speculative future use.

## Control the change surface

- Implement the missing behavior, not every surrounding issue discovered during investigation.
- Separate required dependencies from adjacent improvements. Mention unrelated findings briefly; save them in an agreed location when requested or required by repository practice.
- Make every changed file justify its necessity for behavior, contract compatibility, persistence, traceability, or verification. Multiple repositories may legitimately need changes when a shared contract crosses their boundary.
- Keep applicable channel variants and consumers consistent. Do not call an incomplete cross-layer patch minimal just because it touches fewer files.
- If the patch expands unexpectedly, reassess its design before adding more machinery. Explain unavoidable scope growth and obtain direction for material expansions beyond the request.
- When simplifying an earlier patch, remove only changes attributable to that work; preserve unrelated user changes.

## Verify the behavior and its boundaries

- Test the desired outcome, not just the chosen implementation's wording or internal helper.
- Include relevant counterexamples: incomplete evidence, stale state, changed preferences, wrong entity scope, explicit refusal, or repeated execution. Choose boundaries proportional to the actual risk.
- Verify necessary contract consumers and applicable channel variants. Run repository-required checks and distinguish unit coverage from live or model-behavior validation.
- Check that the original failure is addressed without introducing a duplicate mechanism or changing unrelated semantics.

## Hand off concisely

Report the behavior changed, the essential design choice, verification results, and any remaining validation or deferred work. Say what was not verified. Do not imply deployment, live testing, or guaranteed model behavior from local tests alone.

## Design check

Before finishing, ask: “If I removed each new mechanism in turn, would the requested behavior or a required invariant break?” Remove mechanisms without a concrete answer. Keep the ones needed for a correct, maintainable end-to-end solution.
