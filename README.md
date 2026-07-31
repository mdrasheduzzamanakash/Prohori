# Prohori: Detecting Brand-Impersonation Phishing Attacks with Confidence-Gated Domain-Consistency Verification

> **Note (Double-Blind Review):** This repository accompanies an anonymous submission. All author names, affiliations, emails, institutional acknowledgments, and personally identifying links (e.g., personal GitHub/Kaggle accounts) have been removed or redacted from this README and the accompanying code/data release to preserve reviewer anonymity. A de-anonymized version with full attribution, acknowledgments, and links will be released upon acceptance.

---

## Overview

Phishing websites increasingly combine convincing visual impersonation of trusted brands with structural content designed to evade lexical and HTML-based filters. **Prohori** is a phishing detection framework that combines two modules — **GFhD** (a structural classifier) and **ViS** (a visual brand-matching module) — through **confidence-gated domain-consistency verification**.

The system:
1. Recognizes which legitimate brand a webpage visually resembles (ViS).
2. Checks whether that brand's canonical domain matches the page's actual hosting domain.
3. Flags visually convincing but domain-inconsistent clones as impersonation attacks — but **only when the visual match is confident**, as measured by Monte Carlo Dropout uncertainty.
4. Falls back to **GFhD**, a compact 122K-parameter structural classifier, when the visual branch is uncertain.

ViS uses knowledge distillation, compressing a frozen CLIP ViT-B/32 teacher into a lightweight MobileNetV3-Small student (4.8 MB), removing the need for a heavyweight vision-language model at inference time.

### Key Results

| Model | Accuracy | F1-score | ROC-AUC |
|---|---|---|---|
| GFhD alone (general phishing benchmark) | 0.98 | 0.98 | 0.9976 |
| GFhD alone (brand-impersonation subset, 88 sites) | 0.54 | 0.39 | 0.504 |
| **Prohori (fused, ours)** | **0.977** | **0.977** | 0.978 |

GFhD alone achieves near-perfect performance on general phishing detection but drops to chance-level accuracy on convincing brand-impersonation clones, motivating the confidence-gated visual branch. The fused Prohori pipeline recovers to 97.7% accuracy / 0.977 F1 on the same domain-inconsistent impersonation cases.

## Method Summary

- **Structural branch (GFhD):** 53 handcrafted features across three groups — hard (entropy/complexity/fractal-dimension), URL, and HTML/DOM — fed into a 4-layer MLP (256→256→128→64) with batch normalization, ReLU, and dropout (p=0.3).
- **Visual branch (ViS):** MobileNetV3-Small backbone distilled from a frozen CLIP ViT-B/32 teacher via cosine distillation loss. At inference, only the 4.8 MB student and a compact brand-embedding database are used — the CLIP teacher is discarded after training.
- **Uncertainty gating:** K stochastic forward passes with MC-Dropout produce a similarity standard deviation σ̂, converted to a confidence score w ∈ [0, 1].
- **Fusion rule:** If similarity s ≥ τ (τ = 0.80), the visual branch outputs a domain-consistency decision v; final probability is `p_final = (1 − w)·p_GFhD + w·v`. If s < τ, the match is treated as UNKNOWN (w = 0) and Prohori falls back entirely to GFhD.

## Datasets

- **GFhD training/evaluation:** Derived from the publicly available **PhreshPhish** benchmark (see paper references; dataset citation preserved as it is a third-party public resource, not an identifying link).
- **ViS training:** A locally collected set of brand webpages representative of regionally targeted services.
- **Impersonation evaluation set:** A curated set of 88 sites (45 legitimate, 43 spoofed brand clones), expanded to 1,935 legitimate/phishing page variants for the structural-only ablation, and augmented to 2,000+ pages for the full impersonation benchmark.

> Dataset download links and any code-hosting account identifiers have been withheld from this anonymized release. Reviewers requiring dataset access for verification may use the anonymized supplementary materials link provided in the submission portal, if applicable.

## Limitations

Prohori relies on full-page screenshot embeddings, which can drift as legitimate webpages redesign over time. The similarity threshold (τ = 0.80) is also sensitive to strong rotation/perspective distortion. The impersonation evaluation set is regionally focused; broader benchmarks are needed to confirm generalization. See the paper's Limitations section for full discussion.

## Ethics Note

All interface examples referenced in this work are simplified wireframe reconstructions created for illustrative purposes and do not depict actual captured phishing pages, real user data, or functional code.

## License

License to be finalized upon acceptance.

## Citation

Citation details withheld for double-blind review. A full BibTeX entry will be provided upon publication.
