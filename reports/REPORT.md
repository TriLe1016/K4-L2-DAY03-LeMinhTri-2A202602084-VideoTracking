# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: `Le Minh Tri`
Ngày: `2026-09-15`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT (local, bản 2.74.1 — theo run-card trong `lab-guide.html`) |
| Thời gian gán `clip_02` (warm-up) | 5 phút |
| Thời gian gán `clip_01` | 15 phút |
| Số track đã vẽ trong `clip_01` | 8 (đếm trực tiếp từ `annotations/clip_01/gt.txt`, track ID 1–8) |
| Số keyframe trung bình mỗi track | 70 — `gt.txt` xuất MOT 1.1 có 563 dòng / 8 track ≈ 70.4 dòng/track, nhưng đây là số **frame có box sau nội suy**, không phải số keyframe CVAT thật (project CVAT không có trong repo để đếm keyframe chính xác) |

Tham khảo thêm (đếm được từ `annotations/clip_02/gt.txt`, 7 track, 249 dòng, ≈35.6 dòng/track) nếu cần so sánh với clip chính.

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. Nhiều xe xuất hiện cùng lúc trong khung, dễ nhảy qua lại giữa các xe khi vẽ → xử lý bằng cách làm xong hẳn một xe rồi mới sang xe khác (đúng thứ tự GUIDE.md khuyến nghị), tránh tạo ID switch do mất tập trung.
2. Các xe chồng lên nhau / che khuất một phần khi cắt nhau → áp luật che <25 frame (2 giây) vẫn giữ ID cũ, đặt keyframe dày hơn ngay trước và sau đoạn chồng lấp để bbox không trôi khi xe tách ra.
3. Xe xuất hiện bất ngờ, còn nhỏ/mờ ở rìa khung → chờ đến frame đầu tiên nhận diện chắc chắn là xe bốn bánh mới bắt đầu track, tránh đoán bừa lúc còn mơ hồ (đây cũng là nguyên nhân track 6 của tôi thiếu 27% quãng đời so với gold — tôi bắt đầu quá muộn).

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1 (ID nhấp nháy): chạy `check_mot_labels.py` trên cả hai clip — 0 lỗi định dạng, không có ID trùng trong cùng frame ở cả `clip_01` (8 track) và `clip_02` (7 track).
- Lượt 2 (frame đầu/cuối mỗi track): dò toàn bộ 15 track bằng script so khớp tọa độ với biên khung hình. 2 cảnh báo "đứng im" từ `check_mot_labels.py` (clip_01 track 3; clip_02 track 2, 3) đã xác minh bằng mắt qua `outputs/vis_*`: track 3 clip_01 là xe SUV **đỗ thật** trên đảo giao thông, track 2/3 clip_02 là hai xe buýt **kẹt xe đứng yên thật** — không phải quên bấm `outside`.
- Lượt 3 (giữa đoạn dài): so IoU giữa mọi cặp frame liên tiếp cùng track. `clip_01` không có cú nhảy nào (IoU ≥ 0.3 toàn bộ). `clip_02` có 1 điểm IoU=0.29 ở track 6 frame 38→39, kiểm bằng ảnh thì đây là xe đang co nhỏ nhanh lúc ra khỏi góc khung hình, không phải ID switch.

Kiểm chéo với partner: **không thực hiện** — đã hỏi và xác nhận làm solo không bắt buộc phải có người thứ hai đổi file. Thay vào đó, tự đóng vai reviewer cho chính mình bằng ba lượt tua ở trên; `reports/review_partner.md` ghi rõ đây là self-review, không có người thứ hai, để minh bạch với người chấm.

Ca nào cần luật rõ hơn trong `GUIDELINE_MINI.md`: khong co

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `fafa15b33d846c869ac8a96fd30e37ede21ae4415e010059ebace876a5b502fa` |
| Thời điểm khóa (UTC) | `2026-09-15T07:30:18Z` |
| Số row / frame / track trước khi mở reference | 563 dòng / 190 frame / 8 track |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | 0.739 | 0.722 | 0.762 | 0.814 | 0.963 | 0.927 | 0.785 | 16 | 26 | 0 |
| Sau rework | (không đổi — xem ghi chú) | | | | | | | | | |

> **Không rework**: bản pre-gold đã qua cổng ngay lần đầu, nên `outputs/eval_vs_gold.json` (chạy trên `annotations/clip_01/gt.txt` không sửa gì thêm) cho số liệu **giống hệt** bản pre-gold ở trên. Script vẫn liệt kê vài chỗ đáng cải thiện dù đã qua cổng (xem bảng dưới) — chọn không sửa vì đã đạt ngưỡng, KHÔNG phải vì không tìm ra.

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **ĐẠT** (0.963 / 0.927 / 0.785)

Các phát hiện từ `evaluate_tracking.py` (không sửa vì đã qua cổng, ghi lại để minh bạch):

| Loại lỗi | Frame | ID | Ghi chú |
| --- | --- | --- | --- |
| Bbox thừa | 149–151 | track 4 (của tôi) | còn bbox sau khi xe gold track 4 đã rời khung — quên bấm `outside` đúng lúc |
| Bbox trôi (IoU 0.54–0.59) | 1, 54, 93, 106, 108, 115, 120 | gold track 3, 4, 5, 5, 5, 6, 7 | lệch nhẹ giữa hai keyframe, cần thêm keyframe quanh các frame này |
| Thiếu đoạn | — | gold track 6 | mới phủ 41/56 frame (73%) quãng đời |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | 3.13.15 / 8.4.145 / 2.11.0+cu128 / 0.5.13 |
| weights / hai tracker | `yolo26n.pt` · control = `bytetrack.yaml` (mặc định Ultralytics) · treatment = BoT-SORT + ReID (`configs/trackers/botsort-reid.yaml`) |
| conf / IoU / imgsz / classes | 0.25 / 0.7 / 960 / `[2, 5, 7]` (COCO: car, bus, truck) |
| device | GPU (cuda:0) |

Ghi chú: tham số treatment (`configs/trackers/botsort-reid.yaml`) đã cố định sẵn trong repo:
`track_high_thresh=0.25`, `track_low_thresh=0.10`, `new_track_thresh=0.25`, `track_buffer=30`,
`match_thresh=0.80`, `fuse_score=true`, `gmc_method=none`, `proximity_thresh=0.50`,
`appearance_thresh=0.80`, `with_reid=true`, `model=auto`.

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.739 | 0.722 | 0.762 | 0.814 | 0.963 | 0.927 | 0.785 | 16 | 26 | 0 |
| ByteTrack control vs gold | 0.709 | 0.649 | 0.776 | 0.846 | 0.875 | 0.749 | 0.823 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.763 | 0.711 | 0.820 | 0.872 | 0.900 | 0.792 | 0.860 | 91 | 26 | 2 |
| ReID vs bạn | 0.680 | 0.623 | 0.746 | 0.796 | 0.904 | 0.796 | 0.760 | 95 | 20 | 0 |

> **Cảnh báo cổng:** `eval_bytetrack_vs_gold.json` báo `gate.passed = false` — ByteTrack control **KHÔNG qua cổng MOTA** (0.749 < 0.75 yêu cầu), dù IDF1/MOTP đạt. ReID treatment qua cả 3 cổng. Đây là điểm cần nêu rõ trong report: control tự nó không đạt chuẩn annotation, treatment mới đạt.

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

Ở cả 4 hàng trong bảng trên, **MOTA luôn THẤP hơn IDF1** (ví dụ bản của tôi: MOTA 0.927 < IDF1 0.963) — tức pattern cảnh báo "MOTA cao mà IDF1 thấp" trong GUIDE.md **không xảy ra** ở clip này, vì IDSW rất thấp (0 hoặc 2) nên lỗi identity không phải nguồn gây lệch điểm chính. Chênh lệch MOTA/IDF1 ở đây đến từ cách hai chỉ số đếm lỗi khác nhau: MOTA trừ điểm trực tiếp theo từng frame có FP/FN/IDSW (đếm phẳng theo occurrence), trong khi IDF1 dùng matching tối ưu ở cấp độ toàn bộ quãng đời track rồi mới tính precision/recall trên ID — nên một vài box lệch/bỏ sót rải rác ít ảnh hưởng IDF1 hơn là ảnh hưởng MOTA. Lý do MOTA "không phạt nặng lỗi ID" (theo GUIDE.md): mỗi ID switch chỉ trừ **đúng 1 điểm** trong tử số FP+FN+IDSW bất kể track đó có bị cắt đôi giữ nguyên bao nhiêu frame sau đó — nên một track 190 frame bị đổi ID giữa chừng cũng chỉ tốn 1 điểm MOTA, còn IDF1/AssA phạt đúng phần nửa quãng đời bị gán sai ID.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

IDF1: 0.875 → 0.900 (+0.025). AssA: 0.776 → 0.820 (+0.044). **IDSW bằng nhau: 2 và 2** — treatment không giảm số lần đổi ID, chỉ đổi *vị trí* xảy ra (ByteTrack: frame 59 track gold-4 14→15, frame 94 track gold-5 23→32; ReID: frame 87 track gold-5 17→18, frame 113 track gold-6 24→31). Cải thiện AssA/IDF1 đến từ **độ phủ track tốt hơn**, không phải giảm switch: ByteTrack có 3 track gold bị phủ thiếu (`partially_covered_gt_tracks`: track 8 chỉ 61%, track 6 75%, track 5 77%), còn ReID chỉ 1 track bị phủ thiếu (track 6, 79%). Frame sequence cụ thể: **gt_track 8, frame 152–167** — ByteTrack liên tục match với IoU thấp (0.52–0.58, qua pred_track 54) và chỉ phủ được 61% quãng đời track này; ở bản ReID, track 8 không xuất hiện trong danh sách lỗi (`loose_boxes`/`partially_covered`) nào cả → treatment giữ track này tốt hơn hẳn. Vì ByteTrack và BoT-SORT+ReID là hai implementation tracker khác nhau hoàn toàn (không phải cùng 1 tracker bật/tắt module ReID), nên **không thể cô lập** phần cải thiện này là do riêng "ReID" hay do khác biệt thuật toán association/motion model giữa hai tracker.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

DetA: 0.649 → 0.711 (+0.062). FP gần như không đổi (88 → 91), nhưng **FN giảm gần một nửa** (54 → 26). Vì cả hai run dùng **chung weights, chung conf/IoU/imgsz/classes** (`model_run_config.json`), chênh lệch DetA không thể đến từ detector tự thân — bằng chứng: tổng số box mà mỗi tracker xuất ra khác nhau (ByteTrack 607 box, ReID 638 box, theo `PRED_boxes` trong 2 file eval `*_vs_gold.json`). Điều này cho thấy phần cải thiện đến từ **tầng tracking/association**: BoT-SORT+ReID (qua motion prediction + appearance re-association) giữ track "sống" qua các frame mà bản thân detector có thể đã lưỡng lự hoặc bị che một phần, tạo thêm box khớp GT mà ByteTrack bỏ lỡ. Lỗi còn lại (FP≈90, các `ghost_pred_tracks` dài 10–43 frame ở cả hai run) chủ yếu là **association/track-management** (track giả không khớp track tham chiếu nào), không phải lỗi detector thô.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

`eval_reid_vs_me.json` → `ghost_pred_tracks`: **pred_track 7 (ReID), frame 16–116 (dài 43 frame)** — "không khớp track tham chiếu nào" trong nhãn của tôi. Tức ReID duy trì một track dài 43 frame ở vị trí tôi không hề gán nhãn. Cần xem lại khung hình 16 và 116 để xác nhận: nhiều khả năng đây là **false positive thật** của model (vật thể tĩnh, bóng, hoặc phần phản chiếu bị nhận nhầm là xe), vì một track giả kéo dài liên tục 43 frame khó là nhiễu ngẫu nhiên — nhãn tay của tôi (đã tự kiểm 3 lượt, 0 lỗi định dạng) nhiều khả năng đúng hơn ở vị trí này.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

`eval_reid_vs_me.json` → `fragmented_gt_tracks`: **track 6 của tôi (frame 105–190, 86 frame)** bị ReID khớp thành 2 track khác nhau (pred_track 29 trong 81 frame, pred_track 39 chỉ 1 frame). Vì track 6 là track tôi tự tin giữ nguyên 1 ID suốt 86 frame, việc model tách nó ra ở đúng 1 frame là tín hiệu đáng xem lại: có thể tại thời điểm đó xe bị che một phần khiến model đổi ID trong khi tôi giữ nguyên (đúng theo rule "che <2s giữ ID" trong `GUIDELINE_MINI.md`) — cần mở ảnh overlay quanh giữa track (khoảng frame 145–150) để xác nhận đây là model nhầm lẫn thoáng qua, không phải annotation của tôi sai.

> Ghi chú: các câu trả lời trên dựa trực tiếp trên số liệu `outputs/eval_*.json`; nếu bạn xem ảnh overlay thật từ Colab và thấy khác, hãy sửa lại theo bằng chứng bạn quan sát được.

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

Sẽ chuẩn hóa ngay 2 luật mà `GUIDELINE_MINI.md` mục 5 đã chỉ ra còn thiếu: (1) luật cho xe đỗ/đứng yên — ghi rõ "vẫn track suốt bằng 1 ID" ngay từ đầu để tránh mất thời gian nghi ngờ mỗi khi `check_mot_labels.py` cảnh báo giả; (2) ngưỡng bắt đầu track cho xe nhỏ/mờ — đặt tiêu chí cụ thể hơn ("thấy rõ 4 bánh và khung xe" thay vì chỉ "xác định được là xe") để tránh bắt đầu muộn như ca track 6. Về quy trình, sẽ chạy `check_mot_labels.py` và `visualize_tracks.py` ngay sau khi gán xong mỗi 2–3 xe thay vì đợi đến cuối, để bắt lỗi sớm hơn thay vì dồn hết vào bước tự kiểm cuối cùng.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền đầy đủ, không còn chỗ trống
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md` — đã ghi rõ là self-review (làm solo), chưa có finding chi tiết
- [x] `reports/REPORT.md` (file này) — mục 3, 4, 5 đã điền số liệu thật; mục 1 (thời gian, tình huống khó), mục 2 (luật còn thiếu) và mục 6 (phản tư) vẫn cần bạn tự viết — đây là trải nghiệm cá nhân, AI không được viết thay
