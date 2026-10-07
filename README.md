# Lightweight and Interpretable Phishing Website Detection

MSc Computer Science research project investigating whether a compact and interpretable combination of conventional URL features and URL–HTML registered-domain-consistency features can improve phishing website detection under temporal change.

The project uses **PhreshPhish v1.0.1**, **Logistic Regression**, and **Random Forest**, with a leakage-controlled temporal evaluation design.

---

## Research Question

> To what extent can a compact and interpretable set of URL–HTML registered-domain-consistency features improve lightweight phishing website detection beyond conventional URL features when evaluated on temporally separated webpages?

---

## Core Experimental Comparison

The primary comparison is:

**URL-only models**

versus

**URL + HTML domain-consistency models**

The project does not claim to invent domain-matching features. Instead, it evaluates whether a deliberately compact and interpretable feature representation provides measurable benefit under temporal change.

---

## Dataset

This project uses:

**PhreshPhish v1.0.1**

Dataset:
https://huggingface.co/datasets/phreshphish/phreshphish

Paper:
https://arxiv.org/abs/2507.10854

Dataset revision used in Notebook 1:

```text
eabec4b7a66324b79cc8a0ad856d1731dc26fe1a