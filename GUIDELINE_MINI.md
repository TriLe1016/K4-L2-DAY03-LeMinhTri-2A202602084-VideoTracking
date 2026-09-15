# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `LeMinhTri — solo`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): không có — làm solo, theo đúng bảng mặc định của lab.

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 25 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | dưới 2 giây là occlusion tạm thời, xe vẫn là cùng một xe thật; quá ngưỡng này rủi ro nhầm với xe khác đi vào đúng vị trí đó |
| Xe bị che lâu hơn ngưỡng trên | mở track mới, không cố nối lại ID cũ | quá 2 giây không đủ tin cậy để khẳng định đây vẫn là xe cũ chỉ bằng mắt |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | ra khỏi khung là kết thúc vòng đời track; xe "quay lại" có thể là xe khác trông giống |
| Hai xe cắt nhau / chồng lên nhau | giữ ID theo hướng di chuyển trước khi chồng lấp — xe nào đang đi theo quỹ đạo nào thì tiếp tục đúng ID đó sau khi tách ra | dùng motion liên tục làm tín hiệu chính vì ngoại hình lúc chồng lấp không đáng tin |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên nhận diện chắc chắn là xe bốn bánh (không phải lúc chỉ thấy 1 chấm nhỏ mơ hồ); ngưỡng nhóm chọn: `chua gan voi` — cần xác nhận lại theo trí nhớ thật lúc gán track 6 clip_01 (bản gold phủ nhiều hơn bản của tôi 27%, khả năng tôi bắt đầu track muộn hơn) |
| Xe đang đỗ, không di chuyển | vẫn track suốt thời gian xe còn trong khung, dùng 1 ID duy nhất dù bbox gần như không đổi vị trí (ca thật: track 3 clip_01 — SUV trắng đỗ trên đảo giao thông, đứng yên từ frame 1–190) |
| Keyframe đặt dày ở đâu | dày khi xe rẽ, phanh, hoặc bị che một phần; thưa khi xe đi thẳng đều tốc độ ổn định |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01`, frame 1–190, track 3
- Tình huống: SUV trắng đỗ nguyên chỗ suốt cả clip; `check_mot_labels.py` tự động nghi ngờ "bbox đứng im — quên bấm outside?"
- Quyết định: giu nguyen — bạn có giữ 1 ID suốt 190 frame không, hay có tách đoạn? xác nhận lại theo thao tác thật
- Lý do: van la vat the 

### Ca 2
- Clip / frame / ID: `clip_02`, frame 1–15+, track 2 và track 3
- Tình huống: hai xe buýt gần như không di chuyển do kẹt xe, dễ nhầm là lỗi giống ca 1
- Quyết định: van gan
- Lý do: van la vat the

### Ca 3
- Clip / frame / ID: `clip_01`, track 6 (đối chiếu với gold) — bản của tôi chỉ phủ 41/56 frame (73%) quãng đời track này theo `outputs/eval_pre_gold.json`
- Tình huống: có một đoạn xe xuất hiện trong gold mà tôi chưa gán — khả năng cao là lúc xe còn nhỏ/mờ ở đầu hoặc cuối track, đúng ca "ngưỡng bắt đầu track" ở mục 3
- Quyết định: khong gan — nhớ lại lúc đó ngần ngại gán vì lý do gì
- Lý do: chua xac dinh duoc

> Nếu bạn nhớ có ca nào khác rõ hơn (ví dụ 2 xe thật sự cắt nhau, hoặc 1 xe bị che hơn 2 giây), ưu tiên thay bằng ca đó — mục này cần trải nghiệm thật, càng cụ thể càng tốt.

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- Luật "xe đỗ/đứng yên" cần ghi rõ ngay từ đầu ở mục 2/3, vì lúc gán không chắc có nên track xe đỗ suốt clip hay không — dẫn tới cảnh báo giả từ `check_mot_labels.py`.
- Luật về ngưỡng bắt đầu track cho xe nhỏ/mờ cần cụ thể hơn — track 6 (gold) bị thiếu 27% quãng đời cho thấy tôi bắt đầu track muộn hơn gold.
