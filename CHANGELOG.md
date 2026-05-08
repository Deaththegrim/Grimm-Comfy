# Grimm-Comfy Changelog

Fork of [Comfy-Org/ComfyUI](https://github.com/Comfy-Org/ComfyUI) maintained by [@Deaththegrim](https://github.com/Deaththegrim). Tracks fork-specific work; upstream changes follow the parent repo's history.

The integration branch `grimm-all` carries every fork patch merged on top of upstream `master`. Individual feature branches (one per upstream PR) are kept so they can be cleanly fast-forwarded as PRs land.

---

## 2026-05-07 — Mega session: 30 upstream PRs + local tooling

All 30 PRs opened in a single day from this fork against `Comfy-Org/ComfyUI`. Every branch is rebased on upstream `master` and reviewed by CodeRabbit; iteration commits already in for #13754, #13756, #13757, #13759, #13762, #13763, #13764, #13766.

**Status as of 2026-05-07:** all 30 PRs gated on first-time-contributor Action approval. `grimm-all` is ~80 commits ahead of upstream `master`, sync-tested with `python main.py --disable-smart-memory --cpu --quick-test-for-ci` (clean boot), end-to-end SDXL workflow exercised on the live server (768×768, 20-step Euler, 20.73 s).

### Performance (8 PRs)

| PR | Branch | Summary |
|---|---|---|
| [#13754](https://github.com/Comfy-Org/ComfyUI/pull/13754) | `clip-encode-cache` | CLIPTextEncode LRU. RLock + weakref purge after CodeRabbit r1+r2. |
| [#13756](https://github.com/Comfy-Org/ComfyUI/pull/13756) | `saveimage-parallel-encode` | 3.7× parallel PNG encode via `ThreadPoolExecutor`; fail-fast cancel after CodeRabbit. |
| [#13768](https://github.com/Comfy-Org/ComfyUI/pull/13768) | `async-vae-decode` | Pinned-host `non_blocking` D2H copy on CUDA→CPU (refs upstream #1147). |
| [#13771](https://github.com/Comfy-Org/ComfyUI/pull/13771) | `lora-process-cache` | Process-wide LRU for LoRA state-dicts (cap=4). |
| [#13774](https://github.com/Comfy-Org/ComfyUI/pull/13774) | `async-vae-encode` | Pinned-host D2H on `VAE.encode` (mirror of #13768). |
| [#13778](https://github.com/Comfy-Org/ComfyUI/pull/13778) | `controlnet-loader-cache` | Process-wide LRU for ControlNet state-dicts (cap=2). |
| [#13779](https://github.com/Comfy-Org/ComfyUI/pull/13779) | `vae-loader-cache` | Process-wide LRU for VAE state-dicts (cap=2). |
| [#13780](https://github.com/Comfy-Org/ComfyUI/pull/13780) | `upscale-loader-cache` | Process-wide LRU for UpscaleModel state-dicts (cap=2). |

### Features (6 PRs)

| PR | Branch | Summary |
|---|---|---|
| [#13758](https://github.com/Comfy-Org/ComfyUI/pull/13758) | `loadimage-subfolders` | Recursive listing under `input/` (closes upstream #1220, +21). |
| [#13759](https://github.com/Comfy-Org/ComfyUI/pull/13759) | `no-cache-models` | `--no-cache-models` startup flag (closes #9250, +18); `queue_item` gate added after CodeRabbit. |
| [#13761](https://github.com/Comfy-Org/ComfyUI/pull/13761) | `pause-queue` | `PromptQueue.paused` + `/queue/pause` + `/queue/resume` (closes #1032, +26). |
| [#13762](https://github.com/Comfy-Org/ComfyUI/pull/13762) | `basic-auth` | `--username` / `--password` HTTP Basic with CORS-safe 401 (closes #987, +26). |
| [#13769](https://github.com/Comfy-Org/ComfyUI/pull/13769) | `cache-score-eviction` | New `--cache-score` ScoreCache; evicts by `age × size / exec_time` (closes #8367 — only `help wanted` issue). |
| [#13781](https://github.com/Comfy-Org/ComfyUI/pull/13781) | `saveimage-format-option` | Optional png/jpg/webp + quality on SaveImage; EXIF UserComment carries workflow JSON. |

### API surface (4 PRs)

| PR | Branch | Summary |
|---|---|---|
| [#13757](https://github.com/Comfy-Org/ComfyUI/pull/13757) | `free-memory-endpoint` | `POST /free_memory` sync + `asyncio.to_thread` + TOCTOU re-check. |
| [#13764](https://github.com/Comfy-Org/ComfyUI/pull/13764) | `workflow-to-prompt-api` | `POST /workflow_to_prompt` server-side `graphToPrompt` (closes #1112, +16); NEVER-mode test + node-wide try/except after CodeRabbit. |
| [#13777](https://github.com/Comfy-Org/ComfyUI/pull/13777) | `history-clear-count` | `POST /history` returns `{cleared, deleted}` JSON. |
| [#13782](https://github.com/Comfy-Org/ComfyUI/pull/13782) | `interrupt-404` | `POST /interrupt` returns 404 when `prompt_id` doesn't match running. |

### Bug fixes (4 PRs)

| PR | Branch | Summary |
|---|---|---|
| [#13766](https://github.com/Comfy-Org/ComfyUI/pull/13766) | `fix-vram-sentinel-oom` | Replace 1e32 sentinel with `get_free_memory(device)` for Jetson / unified memory (closes #11332, +7); DirectML special-case after CodeRabbit. |
| [#13770](https://github.com/Comfy-Org/ComfyUI/pull/13770) | `fix-imageaddnoise-global-rng` | Replace `torch.manual_seed(seed)` with per-call generator so the global RNG is no longer polluted. |
| [#13772](https://github.com/Comfy-Org/ComfyUI/pull/13772) | `fix-feathermask-off-by-one` | FeatherMask right/bottom feather hit columns 0/0 instead of -1/-1; rightmost column never feathered, leftmost double-feathered. |
| [#13784](https://github.com/Comfy-Org/ComfyUI/pull/13784) | `samplers-random-inpaint-generator` | `samplers.py` random-inpaint per-call `torch.Generator` (no global-RNG mutation). |

### DX / error messages (8 PRs)

| PR | Branch | Summary |
|---|---|---|
| [#13760](https://github.com/Comfy-Org/ComfyUI/pull/13760) | `friendly-node-errors` | `node_id` + class in failure log; 3 new shape/OOM tips. |
| [#13763](https://github.com/Comfy-Org/ComfyUI/pull/13763) | `friendlier-checkpoint-error` | "Could not detect model type" hints for Flux/SD3/VAE/TE/GGUF (closes #4329, +21); GGUF-before-TE ordering after CodeRabbit. |
| [#13765](https://github.com/Comfy-Org/ComfyUI/pull/13765) | `sentencepiece-friendly-error` | Actionable `ImportError` when `sentencepiece` is missing (refs #7744, +14). |
| [#13773](https://github.com/Comfy-Org/ComfyUI/pull/13773) | `is-changed-warning-context` | Add `node_id` + class to `IS_CHANGED` failure warning. |
| [#13775](https://github.com/Comfy-Org/ComfyUI/pull/13775) | `fix-portrduff-assert` | `PorterDuffImageComposite` bare `assert` → `ValueError` with channel counts. |
| [#13776](https://github.com/Comfy-Org/ComfyUI/pull/13776) | `view-metadata-bad-json` | `view_metadata`: 422 instead of 500 on malformed safetensors header. |
| [#13783](https://github.com/Comfy-Org/ComfyUI/pull/13783) | `assert-to-valueerror-batch` | `nodes_lt` + `nodes_lt_audio` user-facing asserts → `ValueError` with actual numbers. |
| [#13785](https://github.com/Comfy-Org/ComfyUI/pull/13785) | `lora-extract-bare-except` | `nodes_lora_extract` bare `except:` → `except Exception:` (Ctrl-C survives). |

### Local-only tooling (not upstreamed)

Lives in `/home/junie/comfy/ComfyUI/` on the dev box; useful for anyone running the `grimm-all` branch with similar hardware (RX 9070 XT, 32 GB system RAM).

| Script | Purpose |
|---|---|
| `launch.sh` | Updated to use `--cache-score 8` (PR #13769) plus env-var caps for the new LRU caches: `COMFY_CLIP_ENCODE_CACHE_MAX=64`, `COMFY_LORA_CACHE_MAX=8`, `COMFY_CONTROLNET_CACHE_MAX=2`, `COMFY_VAE_CACHE_MAX=2`, `COMFY_UPSCALE_CACHE_MAX=2`. |
| `restart-comfy.sh` | New. SIGTERM running comfy → 10 s grace → SIGKILL → port-occupancy check → relaunch. Recovery path when the GPU wedges. |
| `comfy-cli.sh` | New. Thin wrapper for the new endpoints — `status` / `queue` / `pause` / `resume` / `interrupt` / `free` / `history` / `ping`. End-to-end verified against the running 8188 server; pause/resume returns the `was_paused` field correctly. |

### Environment notes

- `comfy-aimdo` upgraded `0.2.12 → 0.3.0` (the `vram_buffer.py` module was missing in 0.2.12 and broke `import comfy.model_management`).

### Deferred / not changed

The remaining `torch.manual_seed` antipattern still has 3 instances in load-bearing reproducibility paths — explicitly flagged in PR #13784, **NOT** changed:

- `comfy/sample.py:prepare_noise` — main KSampler noise generator
- `comfy/k_diffusion/sampling.py:1891` — block-noise after seeding (uses global directly)
- `comfy/ldm/modules/diffusionmodules/upscaling.py:q_sample`

Changing any of these would shift the noise sequence for existing workflow JSON, breaking saved seed→image mappings in the wild. Needs maintainer discussion before touching.

### Branches started but abandoned

| Branch | Reason |
|---|---|
| `common-upscale-skip-narrow` | `narrow()` is a view, not a copy; no perf gain. |
| `fix-cache-update-keyerror` | Already fixed in upstream master commit `4739d77`. |
| `fix-state-dict-no-weight` | Already fixed in upstream master. |
| `history-pagination` | `?max_items` + `?offset` already supported. |

### Triage-only

- Upstream issue [#11400](https://github.com/Comfy-Org/ComfyUI/issues/11400) — KSampler preview regression. Reproduced and triaged as a frontend issue; deferred.
