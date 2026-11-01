# Decisions

Numbered choices that differ from the design brief or fill a gap in the brief.

1. Gemma Scope 2 has no 65k residual SAE at layer 32. The 65k residual SAEs sit at layers 12, 24, 31, and 41. Layer 32 exists under `resid_post_all` at 16k and 262k, in `l0_small` and `l0_big` only. The 12B configuration uses `resid_post_all/layer_32_width_16k_l0_small`, which matches the natural language autoencoder layer and fits beside the model. Checked against the release file list and the safetensors header on 2026-11-01.
2. The clamp formula in the design brief moves from the encoded activation. A JumpReLU encodes zero below threshold while the linear output stays nonzero, so that formula misses the target and fails the acceptance test for serving. The edit here moves from the pre-activation and divides by the feature's encoder-decoder gain.
3. An ablation edits a position only when the feature is above threshold there. Below threshold the feature already reads zero.
4. Gemma 3 12B in bf16 holds about 24 GB of weights, so one NVIDIA L4 cannot hold the model and its activations. The `12b` profile uses two L4s. The `4b` profile uses one and matches the pilot.
5. The checker is a deterministic stand-in for Lean 4. It evaluates closed arithmetic comparisons only, as the design brief allows.
6. Natural language autoencoder readouts are not wired in. The said versus represented gap is reported as not measured, and no value is ever shown for it.
7. Serving uses Hugging Face `transformers` with a forward hook. The vLLM or SGLang path for high-throughput serving is planned.
8. Batch-invariant mode batches only prompts of one length, so no row is padded. Tested on the CPU. It has not been tested on a GPU, where kernel choice can change results.
9. Entries in the signed log carry no wall-clock time, so branches that do identical work have identical hash chains. This makes the determinism test stronger.
10. When a replay withholds a recipient's only incoming message, the recipient stays silent. The edge weight counts the missing activation as zero and records how many replicates this happened in.
11. The test backend is a tiny random Gemma 3 on the CPU. Its text is noise, and every screen that shows it says so. It exists to prove the software works with no GPU, no download, and no credentials.
12. Recorded pilot runs are imported read only. Tests check that the importer reproduces the pilot's published estimates.
13. Each recorded seed draws its own named personas, so per-agent maps and comparisons use one seed at a time.
14. Live models start with no steering vectors. `POST /vectors` builds a persona vector from contrastive statements on the loaded model, since the add operation needs one.
15. A project's global GPU quota, `GPUS_ALL_REGIONS`, often starts at one. The first real provisioning attempt reached this limit, so the user guide documents the limit.
