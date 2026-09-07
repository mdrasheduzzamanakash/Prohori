# Prohori: Detecting Brand-Impersonation Phishing Attacks with Confidence-Gated Domain-Consistency Verification

This repository contains the implementation, and evaluation code for **Prohori**, a phishing detection framework that combines a lightweight structural classifier (GFhD) with a confidence-gated visual brand-matching module (ViS) to detect brand-impersonation phishing attacks.

Accepted at **COMPAS 2026** (Track: Distributed Systems, Networks, and Security).

## Overview

Prohori identifies phishing pages that visually impersonate trusted brands but are hosted on unrelated ("domain-inconsistent") infrastructure. It combines two branches:

- **GFhD** (Generalized Feature-based Phishing Detector) — a compact 122K-parameter MLP that classifies pages using 53 handcrafted URL and HTML/DOM features.
- **ViS** (Visual Similarity module) — a 4.8 MB MobileNetV3-Small backbone, distilled from a frozen CLIP ViT-B/32 teacher, that identifies which brand a rendered screenshot resembles.

A confidence gate, computed via Monte Carlo Dropout uncertainty over ViS, decides when the visual brand match can be trusted. Confident, domain-inconsistent matches are flagged as impersonation; uncertain matches fall back entirely to GFhD.

## Repository Structure
