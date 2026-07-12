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

## `anime.timeline` — kami-eizo-timeline adapter (ADR-2607121400 Wave 4)

`anime.production` deliberately has no duration/timecode attribute for a cut
— that stays with the consuming actor's own entity data. `anime.timeline`
bridges the gap: given an ordered sequence of cut entities (each optionally
carrying a caller-supplied `:cut/duration-frames` and/or `:cut/stage-status`),
`cut-sequence->timeline` produces a real
[`kami-eizo-timeline`](https://github.com/kotoba-lang/kami-eizo-timeline)
value — the canonical EDL data model for the `eizo` domain — with cuts
placed sequentially as clips on one video track.

```clojure
(require '[anime.timeline :as at]
         '[kami.eizo.timeline.timecode :as tc])

(at/cut-sequence->timeline
  [{:cut/id :c1 :cut/duration-frames 48}
   {:cut/id :c2 :cut/duration-frames 24 :cut/stage-status retake-status}]
  {:timebase tc/film-24 :default-duration-frames 12})
```

Two deliberate non-mappings, and why:

- **The 12 production stages are NOT timeline markers.** The stage board is
  explicitly parallel, not serial (`anime.production/stages` docstring:
  "board columns run in parallel") — a stage boundary has no meaningful
  playback-time position, so forcing one onto the timeline would be
  fabricated, not derived. What IS meaningful: a cut whose derived priority
  is `"retake"` (`anime.production/derive-cut-priority`) becomes a real
  timeline marker, since that's a genuine fact about a specific point in the
  cut sequence.
- **The 8 cut-child layers are NOT separate tracks.** They're sequential
  *production states* of one cut's visual content (storyboard → … →
  composite), not simultaneous compositing layers at playback — so one cut
  maps to one clip. A richer source reference (e.g. a real `composite_uri`)
  is left to the caller via `:clip/source-id`, not this adapter's concern.

`cut-sequence->timeline`'s output passes `kami.eizo.timeline/validate-timeline`
— see `test/anime/timeline_test.cljc`.

## Occupation

ISCO-08 `2166` (Graphic and Multimedia Designers — includes animation
designers) —
[cloud-itonami-isco-2166](https://github.com/cloud-itonami/cloud-itonami-isco-2166).

## Test

```bash
clojure -M:test
clojure -M:lint
```

## License

Apache-2.0.
