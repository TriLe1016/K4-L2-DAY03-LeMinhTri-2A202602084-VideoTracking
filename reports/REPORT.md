# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: `Tri Le`
Ngày: `2026-09-15`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT (local, bản 2.74.1 — theo run-card trong `lab-guide.html`) |
| Thời gian gán `clip_02` (warm-up) | `TODO — điền số phút thật, không suy ra được từ repo` phút |
| Thời gian gán `clip_01` | `TODO — điền số phút thật, không suy ra được từ repo` phút |
| Số track đã vẽ trong `clip_01` | 8 (đếm trực tiếp từ `annotations/clip_01/gt.txt`, track ID 1–8) |
| Số keyframe trung bình mỗi track | `TODO` — `gt.txt` xuất MOT 1.1 có 563 dòng / 8 track ≈ 70.4 dòng/track, nhưng đây là số **frame có box sau nội suy**, không phải số keyframe CVAT thật (project CVAT không có trong repo để đếm keyframe chính xác) |

Tham khảo thêm (đếm được từ `annotations/clip_02/gt.txt`, 7 track, 249 dòng, ≈35.6 dòng/track) nếu cần so sánh với clip chính.

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. `TODO — trải nghiệm cá nhân, cần bạn tự điền`
2. `TODO`
3. `TODO`

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: `TODO`
- Lượt 2: `TODO`
- Lượt 3: `TODO`

Kiểm chéo với: `TODO — tên reviewer`. Chi tiết ở `reports/review_partner.md` (hiện chưa có finding nào — file đó cũng đang chờ điền).
Số lỗi bạn tìm được trong bản của bạn ấy: `TODO`. Số lỗi bạn ấy tìm được trong bản của bạn: `TODO`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

`TODO — GUIDELINE_MINI.md hiện vẫn còn 23 chỗ trống "..." chưa điền, nên chưa thể đối chiếu`

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `TODO — file chưa tồn tại, chạy tools/lock_pre_gold.py trước` |
| Thời điểm khóa | `TODO` |
| Số row / frame / track trước khi mở reference | `TODO` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO |
| Sau rework | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO |

> Chưa có `gold/` reference và chưa chạy `tools/evaluate_tracking.py` trong repo này, nên không có số liệu thật để điền — điền bịa vào đây sẽ sai lệch báo cáo.

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **TODO (chưa chạy eval nên chưa xác định được)**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| TODO | TODO | TODO | TODO |
| TODO | TODO | TODO | TODO |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `TODO — điền theo môi trường chạy notebook thật` |
| weights / hai tracker | `TODO weights` · tracker control = ByteTrack (mặc định Ultralytics) · tracker treatment = BoT-SORT + ReID (`configs/trackers/botsort-reid.yaml`, đã có sẵn trong repo — xem ghi chú bên dưới) |
| conf / IoU / imgsz / classes | `TODO — điền theo lệnh chạy notebook thật` |
| device | `TODO` |

Ghi chú: tham số treatment (`configs/trackers/botsort-reid.yaml`) đã cố định sẵn trong repo:
`track_high_thresh=0.25`, `track_low_thresh=0.10`, `new_track_thresh=0.25`, `track_buffer=30`,
`match_thresh=0.80`, `fuse_score=true`, `gmc_method=none`, `proximity_thresh=0.50`,
`appearance_thresh=0.80`, `with_reid=true`, `model=auto`.

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO |
| ByteTrack control vs gold | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO |
| BoT-SORT + ReID vs gold | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO |
| ReID vs bạn | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO | TODO |

> Chưa chạy `notebooks/day3_tracking_yolo_bytetrack.ipynb` / `tools/run_tracker.py` / `tools/evaluate_tracking.py` trong repo này nên không có kết quả model hay số liệu eval thật.

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

`TODO — cần số liệu ở mục 4 trước khi trả lời được`

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

`TODO`

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

`TODO`

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

`TODO`

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

`TODO`

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

`TODO — trải nghiệm cá nhân, cần bạn tự điền`

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [ ] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json` — chưa tồn tại, cần chạy `tools/lock_pre_gold.py`
- [ ] `GUIDELINE_MINI.md` đã điền — hiện còn 23 chỗ `...` trống
- [ ] `outputs/eval_vs_gold.json` — chưa tồn tại
- [ ] `outputs/model_bytetrack_clip_01.txt` — chưa tồn tại
- [ ] `outputs/model_reid_clip_01.txt` — chưa tồn tại
- [ ] `outputs/model_run_config.json` — chưa tồn tại
- [ ] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json` — chưa tồn tại
- [ ] `reports/review_partner.md` — vừa tạo khung, chưa có finding thật
- [ ] `reports/REPORT.md` (file này) — khung đã điền phần đếm được từ repo, còn số liệu eval và câu hỏi phân tích vẫn TODO
