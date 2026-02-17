# Count-Aware Object Detection via Multi-Pass Inference  

---

## Research Objective
This project investigates how inference-time reasoning can improve object detection performance under **strict count-sensitive evaluation**.

In the challenge, an image receives a score of zero if the predicted number of detected objects does not exactly match the ground truth count. This transforms object detection into a **count-constrained structured prediction problem**.

The goal of this work is to design a detection pipeline that produces **stable and exact object counts** in dense and occluded scenes.

---

## Research Hypothesis
Standard object detection models optimize localization and classification confidence but do not explicitly optimize count stability.

We hypothesize that:

> Structured multi-pass inference combined with count stabilization can significantly outperform single-pass detection in count-sensitive evaluation settings.

---

## Methodological Contribution
This work introduces a **count-aware multi-pass inference framework** that separates:

- detection generation  
- duplicate removal  
- count stabilization  
- constrained selection  

Instead of relying on a single prediction pass, the system performs controlled inference variations and aggregates them using statistical reasoning.

---

## Model
- Architecture: YOLOv8 Large
- Training Resolution: 960
- Dataset Format: YOLO
- High-resolution training for small and overlapping objects

Model training improves detection quality, but inference strategy determines count stability.

---

## Multi-Pass Inference Design

Each image is evaluated using multiple confidence and NMS configurations:

| Pass | Purpose |
|---|---|
| Precision | Remove false positives |
| Balanced | Stable baseline |
| Recall | Recover missed detections |
| Extra Recall | Handle dense scenes |

Each pass produces different detection counts.

These variations provide structured evidence for count estimation.

---

## Count Stabilization Strategy

Object count is estimated using:

**Median(count across passes)**

Median is robust to:
- recall overestimation
- precision under-counting

To prevent recall bias, the median is clipped between precision and balanced counts.

This produces a stable target count.

---

## Spatial Deduplication

Bounding boxes are filtered using IoU-based duplicate removal before counting.

This step is critical for dense scenes with heavy overlap.

---

## Constrained Detection Selection

Candidate boxes are pooled from recall and balanced passes.

Each candidate receives a vote bonus if supported by precision detections.

Final score:
confidence + vote bonus

We select **Top-K detections**, where K equals the stabilized count.

This enforces strict count correctness.

---

## Experimental Evaluation

Evaluation performed using Vista-like scoring logic on labeled validation data.

| Metric | Result |
|---|---|
| Vista-like Mean Score | 0.99700 |
| Exact Count Match Rate | 99.70% |
| Mean Similarity (when count matches) | 1.00000 |
| Runtime | 199 sec |
| Throughput | 5.03 images/sec |

---

## Research Findings

1. Detection confidence alone is insufficient for count-sensitive tasks  
2. Multi-pass inference provides structured uncertainty estimation  
3. Median-based aggregation stabilizes prediction counts  
4. Deduplication before counting is critical in dense scenes  
5. Constrained Top-K selection guarantees evaluation compliance  

---

## Significance

This work demonstrates that:

- Evaluation-aware inference design can outperform model scaling
- Object detection can be treated as a constrained optimization problem
- Statistical aggregation improves robustness in dense visual environments

The approach is interpretable, modular, and reproducible.

---

## Repository Contents
