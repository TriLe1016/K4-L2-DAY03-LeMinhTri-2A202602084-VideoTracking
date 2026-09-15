# Peer review — Day 3

Chép file này thành `reports/review_partner.md`. Reviewer chỉ ghi finding; tác giả
tự sửa bài của mình và điền closure.

| Trường | Giá trị |
| --- | --- |
| Author | `Tri Le` |
| Reviewer | `TODO — cần tên người kiểm chéo thật, chưa có trong repo` |
| Pair ID | `TODO` |
| CVAT version | `2.74.1 (local — theo run-card trong lab-guide.html)` |
| Thời điểm review | `TODO` |

## Danh sách finding

Mỗi finding bắt buộc có `frame + ID + lỗi + sửa thế nào`. Nếu chưa thống nhất,
dùng `needs-review`; không ép tác giả sửa theo cảm tính.

| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | --- | --- | --- | --- |
| 1 | TODO | TODO | TODO | TODO | TODO | TODO | TODO |
| 2 | TODO | TODO | TODO | TODO | TODO | TODO | TODO |
| 3 | TODO | TODO | TODO | TODO | TODO | TODO | TODO |

> Chưa có buổi kiểm chéo thật nào diễn ra trong repo này — reviewer cần xem trực tiếp `annotations/clip_01/gt.txt` (8 track, frame 1–190) trong CVAT rồi điền bảng trên.

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | `TODO` — repo có 8 track (ID 1–8) trong `clip_01/gt.txt`, đạt điều kiện tối thiểu 6, nhưng reviewer cần xác nhận trực quan từng track có đúng là xe bốn bánh không | `TODO` |
| Một xe giữ một ID; không reuse ID cho xe khác | `TODO` | `TODO` |
| Occlusion ngắn giữ ID; crossing không đổi ID | `TODO` | `TODO` |
| Entry/exit đúng; không box treo sau khi xe rời khung | `TODO` | `TODO` |
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | `TODO` | `TODO` |
| Frame giữa hai keyframe không bị interpolation drift | `TODO` | `TODO` |
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | PASS — `annotations/clip_01/gt.txt` bắt đầu ở `frame=1`, cột 2 là track ID (1–8), khớp `seqinfo.ini` (`seqLength=190`) | `clip_01/gt.txt` dòng 1: `1,1,118.08,...` |
| Mọi finding có cách sửa và closure do tác giả điền | `TODO` (chưa có finding nào được tạo) | `TODO` |

## Self-QC attestation của reviewer

| Lượt | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence |
| --- | --- | --- |
| 1 — identity/timeline | `TODO` | `TODO` |
| 2 — endpoint/scope | `TODO` | `TODO` |
| 3 — geometry/interpolation | `TODO` | `TODO` |

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: `TODO`
2. Một finding tác giả đóng là `not-a-defect`, kèm lý do (nếu có): `TODO`
3. Một rule cần Lab Coach làm rõ (nếu có): `TODO`
