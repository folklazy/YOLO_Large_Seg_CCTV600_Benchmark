# สรุปผล Largest (X/E) บน CCTV600

## สรุปใน 1 นาที

- ทดสอบ 4 pretrained checkpoints บน 600 ภาพชุดเดียวกันและ GT เข้าเกณฑ์ 769 คน
- Input 640×640, batch 1, FP32 บน Tesla T4 ไม่มี fine-tuning ใน benchmark รอบนี้
- คุณภาพครบ 600 IDs ต่อโมเดล และเวลาครบ 3 clean rounds ต่อโมเดล
- Box AP สูงสุดเป็น yolov9e-seg.pt ที่ 67.808%
- Adapter time ต่ำสุดเป็น yolov9e-seg.pt ที่ 87.289 ms/ภาพ
- Allocated VRAM ต่ำสุดเป็น yolo26x-seg.pt ที่ 702.673 MiB

## ผลลัพธ์หลัก

| Model | Box AP % | AP75 % | Recall % | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26x-seg.pt | 67.201 | 76.046 | 81.144 | 76.028 | 99.223 | 10.078 | 702.673 |
| yolo11x-seg.pt | 64.917 | 73.520 | 76.333 | 71.704 | 98.090 | 10.195 | 737.890 |
| yolov9e-seg.pt | 67.808 | 77.973 | 79.584 | 66.884 | 87.289 | 11.456 | 737.590 |
| yolov8x-seg.pt | 63.481 | 72.864 | 77.763 | 69.740 | 98.263 | 10.177 | 791.839 |

Box AP/P/R/F1 แสดงเป็นเปอร์เซ็นต์ Forward และ adapter เป็น ms/ภาพ, FPS = 1000 / mean adapter ms และ VRAM เป็น peak allocated MiB

## ผู้ชนะของแต่ละด้าน

| ด้าน | โมเดล | ค่าที่วัด |
| --- | --- | --- |
| Box AP | yolov9e-seg.pt | 67.808 % |
| AP75 | yolov9e-seg.pt | 77.973 % |
| Recall | yolo26x-seg.pt | 81.144 % |
| Forward time | yolov9e-seg.pt | 66.884 ms/ภาพ |
| Adapter time | yolov9e-seg.pt | 87.289 ms/ภาพ |
| Adapter FPS | yolov9e-seg.pt | 11.456 ภาพ/วินาที |
| Allocated VRAM | yolo26x-seg.pt | 702.673 MiB |

## สิ่งที่ตัวเลขบอกเรา

- yolov9e-seg.pt มี AP สูงกว่า yolo26x-seg.pt 0.607 จุดเปอร์เซ็นต์
- ตัวนำ AP มี TP 612, FP 53 และ FN 157 ที่ confidence ≥0.25 / bbox IoU ≥0.50
- ตัวนำ AP มี adapter ต่ำสุดในกลุ่มด้วยที่ 87.289 ms/ภาพ
- ตัวนำ AP ใช้ allocated VRAM มากกว่าค่าต่ำสุด 34.917 MiB

## บทบาทของแต่ละโมเดล

| โมเดล | จุดเด่นที่วัดได้ | ข้อแลกเปลี่ยน | เงื่อนไขการเลือก |
| --- | --- | --- | --- |
| yolo26x-seg.pt | Recall สูงสุด, allocated VRAM ต่ำสุดในกลุ่ม | AP ห่างตัวนำ 0.607 pp, adapter เพิ่ม 11.935 ms | เก็บคนให้ครบ, จำกัด allocated VRAM |
| yolo11x-seg.pt | AP 64.917% และ Recall 76.333% | AP ห่างตัวนำ 2.891 pp, Recall ห่างตัวนำ 4.811 pp, adapter เพิ่ม 10.801 ms | ใช้เป็นจุดอ้างอิงของ checkpoint ในกลุ่มนี้ |
| yolov9e-seg.pt | Box AP สูงสุดในกลุ่ม, Precision สูงสุด, forward เร็วสุดในกลุ่ม, adapter เร็วสุดในกลุ่ม | Recall ห่างตัวนำ 1.560 pp, VRAM เพิ่ม 34.917 MiB | เน้น Box AP, ลดสัดส่วนกรอบเกิน, จำกัดเวลา adapter |
| yolov8x-seg.pt | AP 63.481% และ Recall 77.763% | AP ห่างตัวนำ 4.328 pp, Recall ห่างตัวนำ 3.381 pp, adapter เพิ่ม 10.975 ms | ใช้เป็นจุดอ้างอิงของ checkpoint ในกลุ่มนี้ |

## ข้อแลกเปลี่ยนหลัก

### คุณภาพกับความเร็ว

ตัวนำ AP มี AP 67.808% ส่วนตัว adapter เร็วสุดมี AP 67.808% ทั้งสองเป้าหมายอาจได้ checkpoint เดียวกัน ให้พิจารณา forward กับ adapter แยกกัน เพราะ native mask postprocessing มีต้นทุนอยู่หลัง model forward

### คุณภาพกับหน่วยความจำ

ตัว allocated VRAM ต่ำสุดมี AP 67.201% และใช้ 702.673 MiB ค่า allocator peak นี้ครอบคลุม resident model ใน benchmark หลัง warmup ส่วน memory รวมในระบบให้ตรวจ CUDA context และบริการที่รันร่วมด้วย

## ข้อควรระวังในการตีความ

GT ใช้ visible bbox ของคนด้านยาว ≥50 px รวม 769 คน การรับรอง person inventory อิงการตรวจรับของผู้จัดทำ dataset ยังไม่มี independent inventory review ครบชุด ขนาดโมเดลและข้อมูล pretraining ต่างกัน การเปรียบเทียบนี้ยังไม่แยกสาเหตุทาง architecture ไม่มี uncertainty analysis และ Person Mask AP ต้องมี GT silhouette เพิ่ม Adapter time ครอบคลุม preprocessing/H2D, forward และ postprocessing/native-mask transfer โดย input I/O, RLE, writing, model loading และ queue time มีขอบเขตแยก

## อ่านต่อ

[รายงานเทคนิค](REPORT.md) · [การวิเคราะห์ภาพและข้อผิดพลาด](PRESENTATION_SUMMARY_TH.md) · [ตารางเต็ม](metrics/TIER_RESULTS.csv) · [สูตรตัวชี้วัด](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md)
