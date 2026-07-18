# cloud-itonami-isco-8159

Open Occupation Blueprint for **ISCO-08 8159**: Textile, Fur and Leather
Products Machine Operators Not Elsewhere Classified.

This repository designs a forkable OSS business for a textile/fur/leather
products machine-operations plant scheduling and logistics coordination
practice: a plant scheduling and supply-coordination robot manages crew/task
records under a governor-gated actor, so a textile, fur and leather products
machine-operations crew keeps its own operating records instead of renting a
closed workforce-management SaaS.

ISCO-08 8159 is a residual **"Not Elsewhere Classified"** category covering
diverse textile/fur/leather machine-operation work not captured by more
specific ISCO 815x codes (fibre preparing/spinning/winding, weaving/
knitting, sewing, bleaching/dyeing, fur/leather preparing, shoemaking,
laundry). Standard industrial-machine hazards — entanglement, crush injury
from varied machinery — apply generically here, without a single dominant
hazard type.

**Maturity: `:implemented`.** `src/tflcoord/` implements the
`TflCoordActor` as a `langgraph.graph/state-graph` (`tflcoord.actor`) wired
to a `Textile, Fur and Leather Products Machine Operations Coordination
Advisor` (`tflcoord.advisor`) and an independent `TflCoordGovernor`
(`tflcoord.governor`), following the itonami actor pattern
(ADR-2607121000): `:intake -> :advise -> :govern -> :decide -+-> :commit
(:ok? true) +-> :request-approval (:escalate? true, human-in-the-loop
interrupt) +-> :hold (:hard? true)`. HARD invariants (always hold, never
overridable): operator provenance, plant provenance, no-actuation
(`:effect` must be `:propose`), a closed op-allowlist (`:log-work-record`,
`:schedule-crew-operation`, `:flag-safety-concern`,
`:coordinate-supply-order` — nothing else may ever be proposed), and a
permanent, unconditional block on any proposal that would directly finalize
a machine-operation-execution decision (e.g. deciding to proceed with a
specific textile/fur/leather machine run) or a plant-safety-clearance
decision (e.g. declaring a plant or production line safe for operation), or
that would override a plant safety officer's judgment. Always-escalate
paths (human sign-off regardless of confidence, mapping this repo's Trust
Controls in [`docs/business-model.md`](docs/business-model.md)):
`:flag-safety-concern` (always) and `:coordinate-supply-order` above the
registered cost threshold.

## Robotics premise

All cloud-itonami verticals are designed on the premise that a **robot performs
the physical domain work**. Here a plant scheduling/logistics coordination
robot performs crew scheduling, production-run/inventory/progress-record
logging and raw-materials supply-order coordination for a textile, fur and
leather products machine-operations crew, under an actor that proposes
actions and an independent **Textile, Fur and Leather Products Machine
Operations Coordination Governor** that gates them. The governor never
dispatches hardware itself, never operates textile/fur/leather machinery on
the plant floor, and never finalizes a machine-operation-execution decision
or a plant-safety-clearance decision, and never overrides a plant safety
officer's judgment; `:high`/`:safety-critical` actions (such as a flagged
entanglement/crush/equipment-condition concern, or an above-threshold
supply order) require human sign-off. **This actor coordinates PLANT
SCHEDULING/LOGISTICS ONLY — it never operates textile/fur/leather machinery
itself, and it never makes a plant-safety-clearance decision itself.**

Textile, Fur and Leather Products Machine Operators (Not Elsewhere
Classified) run a diverse mix of textile/fur/leather processing machinery
not captured by more specific ISCO 815x codes. This is a generic
industrial-machine hazard domain — entanglement and crush injury from
varied machinery, without a single dominant hazard type; this actor never
operates that equipment and never clears it as safe — it only schedules and
logs around it, and always routes safety concerns to a human plant safety
officer.

## Core Contract

```text
crew roster + plant registration + safety-reporting policy
        |
        v
Textile, Fur and Leather Products Machine Operations Coordination Advisor -> TflCoordGovernor -> log/schedule/coordinate, or human sign-off
        |
        v
robot actions (gated) + operating records + audit ledger
```

No automated advice can dispatch a robot action the governor refuses,
finalize a machine-operation-execution decision, finalize a
plant-safety-clearance decision, override a plant safety officer's
judgment, suppress an operating record, or disclose sensitive data without
governor approval and audit evidence.

## Capability layer

Resolves via [`kotoba-lang/occupation`](https://github.com/kotoba-lang/occupation)
(ISCO-08 `8159`). Required capabilities:

- :robotics
- :identity
- :audit-ledger

See [`docs/business-model.md`](docs/business-model.md) and
[`docs/operator-guide.md`](docs/operator-guide.md).

## License

AGPL-3.0-or-later.
