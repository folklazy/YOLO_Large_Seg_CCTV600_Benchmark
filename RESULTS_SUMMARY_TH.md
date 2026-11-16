# ผลประเมิน YOLO กลุ่ม largest บน CCTV600

Run `cctv600-20261116T073208561699Z` · ตรวจปิดรอบเมื่อ 2026-11-16T16:13:20+07:00 (เวลาไทย)

คุณภาพครบ 4/4 โมเดล ตัวละ 600 ภาพ GT เข้าเกณฑ์ 769 คน คะแนน Box AP สูงสุดในกลุ่มคือ **yolov9e-seg.pt 67.808%**

## คุณภาพกรอบบุคคล

| Model | AP % | AP50 % | AP75 % | P % | R % | F1 % | TP | FP | FN |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26x-seg.pt | 67.201 | 86.080 | 76.046 | 84.668 | 81.144 | 82.869 | 624 | 113 | 145 |
| yolo11x-seg.pt | 64.917 | 84.329 | 73.520 | 86.706 | 76.333 | 81.189 | 587 | 90 | 182 |
| yolov9e-seg.pt | 67.808 | 87.168 | 77.973 | 92.030 | 79.584 | 85.356 | 612 | 53 | 157 |
| yolov8x-seg.pt | 63.481 | 83.016 | 72.864 | 82.030 | 77.763 | 79.840 | 598 | 131 | 171 |

AP ใช้ IoU 0.50–0.95, 101 recall points และ maxDet 200 P/R/F1 ใช้ confidence ≥0.25 และ IoU ≥0.50 จับ valid GT ก่อนพิจารณา ignore union

## ความเร็ว

| Model | Clean rounds | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- |
| yolo26x-seg.pt | 3/3 | 76.028 | 99.223 | 10.078 | 702.673 |
| yolo11x-seg.pt | 3/3 | 71.704 | 98.090 | 10.195 | 737.890 |
| yolov9e-seg.pt | 3/3 | 66.884 | 87.289 | 11.456 | 737.590 |
| yolov8x-seg.pt | 3/3 | 69.740 | 98.263 | 10.177 | 791.839 |

เวลาหลักมาจาก 100 ภาพเดิม × 3 clean rounds หลัง warmup 10 calls ต่อรอบ Adapter รวม preprocess/H2D + forward + postprocess/native-mask transfer ส่วน input read/decode, RLE และเขียนไฟล์วัดแยก FPS = 1000 / mean adapter ms

## กราฟและภาพตัวอย่าง

![PR curve](https://raw.githubusercontent.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/main/metrics/plots/largest_pr_curve.png)

![F1-confidence curve](https://raw.githubusercontent.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/main/metrics/plots/largest_f1_curve.png)

เส้น F1 แสดงผลของ threshold ต่อ trade-off โดย main threshold คง 0.25 ภาพเปรียบเทียบ 16 ภาพเดิมเปิดในเครื่องที่ `outputs/cctv600-20261116T073208561699Z/gallery/index.html` ของ repo นี้

## แหล่งผลและข้อจำกัด

[ตารางกลุ่ม](metrics/TIER_RESULTS.csv) และ [canonical summary](metrics/RESULTS.json) มาจาก saved packets ที่ตรวจรับแล้ว Source/scientific bindings คงเดิม อ่าน [รายงานรวม](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/MASTER_RESULTS.md), [สูตรตัวชี้วัด](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md) และ [ผลตรวจปิดรอบ](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/CLOSEOUT.json)

งานนี้วัด visible bbox บน CCTV600 ของ pretrained segmentation checkpoints ส่วน native masks เก็บเป็น output diagnostics ยังไม่มี GT silhouette สำหรับ Person Mask AP ไม่มี human parsing/color/tracking score และยังไม่มี uncertainty analysis
