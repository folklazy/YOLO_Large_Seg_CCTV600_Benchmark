# ผลประเมิน YOLO บน CCTV600

Run `cctv600-20261116T073208561699Z`

คะแนนหลักเป็น Person Box AP ของ segmentation checkpoints บน visible GT bbox เกณฑ์คนด้านยาว ≥50 px

| Model | Complete | Box AP % | AP50 % | AP75 % | P % | R % | F1 % |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26x-seg.pt | 600/600 | 67.201 | 86.080 | 76.046 | 84.668 | 81.144 | 82.869 |
| yolo11x-seg.pt | 600/600 | 64.917 | 84.329 | 73.520 | 86.706 | 76.333 | 81.189 |
| yolov9e-seg.pt | 600/600 | 67.808 | 87.168 | 77.973 | 92.030 | 79.584 | 85.356 |
| yolov8x-seg.pt | 600/600 | 63.481 | 83.016 | 72.864 | 82.030 | 77.763 | 79.840 |

| Model | Clean rounds | Forward ms/image | Adapter ms/image | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- |
| yolo26x-seg.pt | 3/3 | 76.028 | 99.223 | 10.078 | 702.673 |
| yolo11x-seg.pt | 3/3 | 71.704 | 98.090 | 10.195 | 737.890 |
| yolov9e-seg.pt | 3/3 | 66.884 | 87.289 | 11.456 | 737.590 |
| yolov8x-seg.pt | 3/3 | 69.740 | 98.263 | 10.177 | 791.839 |

Adapter time รวม preprocessing/H2D, forward, NMS/native masks และ compact transfer ส่วน input I/O, RLE และ output writing เก็บแยก

P/R/F1 ใช้confidence≥0.25 และbboxIoU≥0.50 ส่วนAPใช้IoU0.50:0.05:0.95 และmaxDet200 โดยเก็บpredictionsเหนือconfidencefloor0.001

Timing หลักแสดงเมื่อมีสาม clean rounds ครบ งาน GPU อื่นทำให้รอบเวลาเป็น diagnostics

GT person inventory อ้างคำยืนยันตรวจ600ภาพตามกติกาทุกคน≥50px ข้อมูลบางส่วนเคยใช้พัฒนาannotation helper ส่วนผู้เข้าสอบใช้pretrained checkpointที่ตรึง

ภาพจริงและ per-image diagnostics เก็บ private ใน outputs ของ tier ส่วน results JSON/CSV เก็บสถิติรวม

ผลนี้เป็น checkpoint+application configuration comparison ของกรอบคน และยังไม่มีผล Person Mask AP หรือ tracking
