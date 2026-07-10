---
title: "Building a Network Intrusion Detection System"
date: 2025-05-28
draft: false
tags: ["cybersecurity", "python", "machine-learning", "linux"]
summary: "ML-based NIDS using scikit-learn classifiers, K-Means with PCA, and Genetic Algorithm feature selection."
coverImage: "cover.png"
---

## Overview

A machine learning based Network Intrusion Detection System
built to classify network traffic as normal or malicious.

## Tech Stack

- **Classifiers:** Random Forest, SVM, KNN via scikit-learn
- **Clustering:** K-Means with PCA for dimensionality reduction
- **Feature Selection:** Genetic Algorithm
- **Dataset:** NSL-KDD

## How It Works

1. Raw network traffic captured and preprocessed
2. Features extracted and reduced via PCA
3. Genetic Algorithm selects optimal feature subset
4. Classifier trained and evaluated

## Results

Achieved high classification accuracy with reduced
feature set via genetic algorithm optimization.