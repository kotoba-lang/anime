# anime

Work-agnostic anime production pipeline vocabulary — the craft library split
out of gftdcojp's private `ai-gftd-animeka` actor (ADR-2607023000: コードは
kotoba-lang、職能は cloud-itonami-isco、商売は gftdcojp).

The creative atom is the **cut** (a shot, time-axis) — the anime counterpart
of the manga page (`kami-mangaka-*` / `kami-genko`):

```
work → episode → scene → cut → {storyboard layout conceptKeyframe keyframe
                                inbetween colorTrace background composite
                                soundCue}
retake → targets a cut (or a cut child)
```

`anime.production` provides:

- `stages` / `default-stage-status` / `derive-cut-priority` — the 12
  parallel production stages (script … delivery) and per-cut rollup
- `layer-specs` — the 8 generated cut-child layers (record ns, rkey prefix,
  stage advanced, wire envelope keys)
- `schema` — DataScript-style unique-id declarations for the hierarchy
- `collection` / `vertex-uri` / `rkey-from-id` / `slug` — identity helpers,
  parameterized by DID and NSID prefix (no studio authority baked in)

Nothing here knows about a specific studio, work, store, or DID authority —
that wiring stays in the consuming actor.

## Occupation

ISCO-08 `2166` (Graphic and Multimedia Designers — includes animation
designers) —
[cloud-itonami-isco-2166](https://github.com/cloud-itonami/cloud-itonami-isco-2166).

## Test

```bash
clojure -M:test
```

## License

Apache-2.0.
