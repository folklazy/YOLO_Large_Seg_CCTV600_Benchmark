# Largest (X/E) YOLO Segmentation Benchmark — CCTV600

## 1. Experiment Status

COMPLETE for accuracy and dedicated clean timing. Each of 4 models has 600 accepted unique image packets. Evaluation includes 769 eligible person boxes. Dataset-quality and interpretation limitations remain documented below.

## 2. Models Tested

| Family | Checkpoint | Parameters | GFLOPs estimate | Weights MB |
| --- | --- | --- | --- | --- |
| YOLO26 | yolo26x-seg.pt | 70,693,800 | 338.203 | 142.130 |
| YOLO11 | yolo11x-seg.pt | 62,142,656 | 297.892 | 125.091 |
| YOLOv9 | yolov9e-seg.pt | 60,512,800 | 238.333 | 122.212 |
| YOLOv8 | yolov8x-seg.pt | 71,827,888 | 329.189 | 144.102 |

## 3. Protocol Compatibility

| Item | Status and evidence |
| --- | --- |
| Dataset | PASS · 600 exact IDs; dataset membership SHA in frozen config |
| Evaluator | PASS · GT-first bbox matching and shared ignore union |
| Preprocessing | PASS · frozen runtime source binding |
| Input size | 1×3×640×640, centered letterbox |
| Precision | FP32; no AMP/TF32/extra TTA |
| Thresholds | AP floor .001, fixed conf .25, match IoU .50, NMS IoU .70 |
| maxDet | Model 1000 / evaluator 200; cap parity PASS |
| Timing | 3×100 clean observations, warmup 10 each round; sample order verified |
| Environment | Python 3.12.3 / torch 2.14.0+cu130 / Ultralytics 8.4.160 / Tesla T4 |

Compatibility applies within this CCTV600 experiment. MOTS20 supplies the reporting structure and reused detector runtime. CCTV600 uses visible box GT, its ≥50 px eligibility and coarse rectangle-ignore policy. MOTS20 uses person silhouettes and mask matching.

## 4. Overall Results

| Model | Box AP % | AP75 % | Recall % | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26x-seg.pt | 67.201 | 76.046 | 81.144 | 76.028 | 99.223 | 10.078 | 702.673 |
| yolo11x-seg.pt | 64.917 | 73.520 | 76.333 | 71.704 | 98.090 | 10.195 | 737.890 |
| yolov9e-seg.pt | 67.808 | 77.973 | 79.584 | 66.884 | 87.289 | 11.456 | 737.590 |
| yolov8x-seg.pt | 63.481 | 72.864 | 77.763 | 69.740 | 98.263 | 10.177 | 791.839 |

| Model | Precision % | Recall % | F1 % | TP | FP | FN | Ignored | TP-only box IoU |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26x-seg.pt | 84.668 | 81.144 | 82.869 | 624 | 113 | 145 | 26 | 0.882373 |
| yolo11x-seg.pt | 86.706 | 76.333 | 81.189 | 587 | 90 | 182 | 25 | 0.882060 |
| yolov9e-seg.pt | 92.030 | 79.584 | 85.356 | 612 | 53 | 157 | 21 | 0.884462 |
| yolov8x-seg.pt | 82.030 | 77.763 | 79.840 | 598 | 131 | 171 | 24 | 0.876935 |

AP/P/R/F1 columns use percentages. TP-only box IoU is a fraction. Counts use confidence ≥0.25 and box IoU ≥0.50. AP uses 10 IoU thresholds, 101 recall points and maxDet 200. Source-precision values remain in CSV.

## 5. Tier Winners

| ด้าน | โมเดล | ค่าที่วัด |
| --- | --- | --- |
| Box AP | yolov9e-seg.pt | 67.808 % |
| AP75 | yolov9e-seg.pt | 77.973 % |
| Recall | yolo26x-seg.pt | 81.144 % |
| Forward time | yolov9e-seg.pt | 66.884 ms/ภาพ |
| Adapter time | yolov9e-seg.pt | 87.289 ms/ภาพ |
| Adapter FPS | yolov9e-seg.pt | 11.456 ภาพ/วินาที |
| Allocated VRAM | yolo26x-seg.pt | 702.673 MiB |

## 6. Key Findings

The highest Box AP is yolov9e-seg.pt (67.808%), 0.607 percentage points above the runner-up. The shortest adapter mean belongs to yolov9e-seg.pt (87.289 ms/image). Differences are descriptive; no significance or architecture-causality claim is made.

## 7. Per-group Observations

| Model | Day AP % | Night AP % | R 50–99 px % | R 100–199 px % | R ≥200 px % |
| --- | --- | --- | --- | --- | --- |
| yolo26x-seg.pt | 62.582 | 70.873 | 58.989 | 85.673 | 90.763 |
| yolo11x-seg.pt | 58.086 | 70.065 | 51.124 | 80.409 | 88.755 |
| yolov9e-seg.pt | 61.297 | 72.883 | 53.933 | 83.626 | 92.369 |
| yolov8x-seg.pt | 55.143 | 69.743 | 53.933 | 80.994 | 90.361 |

Day/night use existing metadata and contain different scenes and people. Pooled AP is read directly from the canonical full-population result. Size-band recall conditions on GT size and the original matching; precision/AP by size were not computed under a new policy.

## 8. Efficiency and Resource Observations

| Model | Pre mean ms | Post mean ms | Adapter P50 ms | Adapter P95 ms | Adapter std ms | Reserved MiB |
| --- | --- | --- | --- | --- | --- | --- |
| yolo26x-seg.pt | 1.884 | 21.312 | 91.791 | 137.042 | 19.377 | 924.000 |
| yolo11x-seg.pt | 1.910 | 24.475 | 91.360 | 139.101 | 19.703 | 1444.000 |
| yolov9e-seg.pt | 1.958 | 18.446 | 82.202 | 116.488 | 15.023 | 1156.000 |
| yolov8x-seg.pt | 1.960 | 26.563 | 93.476 | 131.799 | 19.427 | 1414.000 |

Means, median, P95 and population standard deviation use all 300 accepted observations. Adapter FPS excludes disk/decode, RLE, packet writing and load/warmup. Native postprocessing substage timers are inclusive subsets. Allocated/reserved peaks include the resident model and take the maximum across the three accepted rounds.

## 9. Warnings and Anomalies

The original study ended with incomplete Nano timing. A linked segment supplied four accepted clean rounds, retaining the original contaminated YOLO11n round and original completion unchanged. Accuracy was not rerun. Detailed accepted round paths and hashes are in the common result index. The owned web worker was restored after authorized temporary suspension.

## 10. Limitations

GT inventory relies on the dataset creator's 600-image acceptance. Independent all-person inventory adjudication remains unavailable. No full-person silhouette GT, mask AP, tracking or parsing/color score is reported here. TP-only IoU excludes missed people. Different pretrained capacities and training histories confound architecture attribution. Related frames and absent uncertainty analysis limit generalization.

## 11. Reproducibility and Source Artifacts

[Canonical tier table](metrics/TIER_RESULTS.csv), [timing statistics](metrics/TIMING_SUMMARY.csv), [shared source/protocol/checkpoint bindings](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/RESULT_INDEX.json), [closed-run checks](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/CLOSEOUT.json), [metric definitions](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md) and [presentation revision evidence](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/PRESENTATION_REVISION.json).

## 12. Relation to Full Scaling Study

[Largest (X/E)](https://github.com/folklazy/YOLO_Large_Seg_CCTV600_Benchmark) · [Second-largest (L/C)](https://github.com/folklazy/YOLO_Second_Largest_Seg_CCTV600_Benchmark) · [Medium (M)](https://github.com/folklazy/YOLO_Medium_Seg_CCTV600_Benchmark) · [Small (S)](https://github.com/folklazy/YOLO_Small_Seg_CCTV600_Benchmark) · [Nano (N)](https://github.com/folklazy/YOLO_Nano_Seg_CCTV600_Benchmark) · [Master](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study)

## Qualitative Analysis

[PRESENTATION_SUMMARY_TH.md](PRESENTATION_SUMMARY_TH.md) discusses four diagnostic cases from the original fixed16. Full frames, identical ROIs and per-GT matching are retained in the private local gallery. Only aggregate charts and de-identified qualitative observations are published.
