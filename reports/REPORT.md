# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: **TODO**
Nhóm: **TODO**
Ngày: **16/09/2026**

## 1. Nhãn của tôi

Số liệu trong phần này lấy tại thời điểm hoàn thành Chặng 4, trước khi rework với gold.

| Chỉ số                       |        Giá trị |
| ---------------------------- | -------------: |
| Số ảnh đã gán                |             20 |
| Số skeleton                  |             27 |
| v=2 / v=1 / v=0              | 304 / 54 / 101 |
| Thời gian trung bình mỗi ảnh |       **TODO** |

Ba khớp có `%v=1` cao nhất:

1. `left_ear`: 6/27 = **22.22%**
2. `right_ear`: 5/27 = **18.52%**
3. `right_hip`: 5/27 = **18.52%**

Các khớp này tương đối phù hợp với những vị trí khó gán. Hai tai thường bị che hoặc khó quan sát khi đầu quay sang bên, nên nhiều trường hợp phải suy ra vị trí và sử dụng `v=1`. Riêng `right_hip` khác ở chỗ điểm hông thường không nhìn thấy trực tiếp do quần áo che, vì vậy vị trí chủ yếu được ước lượng theo cấu trúc giải phẫu thay vì nhìn trực tiếp tâm khớp.

## 2. Chấm với gold

| Chỉ số                | Trước rework | Sau rework |
| --------------------- | -----------: | ---------: |
| OKS trung bình        |       0.8473 |      0.845 |
| OKS@0.50              |       0.8966 |      0.931 |
| OKS@0.75              |       0.7931 |      0.828 |
| Lỗi `dao_trai_phai`   |            1 |          1 |
| Lỗi `nham_nguoi`      |            3 |          3 |
| Lỗi `xoa_khop_bi_che` |           16 |         17 |

Trước rework, evaluator ghép được **27/29 người**, còn thiếu 2 người. Sau rework, số skeleton của bài tăng từ 27 lên 28 và evaluator ghép được **28/29 người**, còn thiếu 1 người.

Mặc dù OKS trung bình giảm nhẹ từ **0.8473 xuống 0.845**, `OKS@0.50` tăng từ **0.8966 lên 0.931** và `OKS@0.75` tăng từ **0.7931 lên 0.828**. Điều này cho thấy số pose đạt hai ngưỡng OKS tăng, nhưng vẫn còn các lỗi keypoint trên một số skeleton nên mean OKS không tăng.

**Tôi đã sửa gì giữa hai lần chạy**

* `train_13.jpg` – người #2 theo kết quả đối sánh của evaluator – toàn bộ 17 keypoint – bổ sung một skeleton người trước đó bị bỏ sót và gán đủ bộ keypoint COCO-17.

Sau rework tôi dừng chỉnh sửa thêm. Vì vậy các lỗi `dao_trai_phai`, `nham_nguoi` và một số trường hợp dùng `v=0` cho khớp bị che vẫn còn trong kết quả cuối.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

Lỗi xảy ra ở **`train_02.jpg`, người #1**. Evaluator cho OKS của skeleton này khoảng **0.392** và phát hiện việc hoán đổi trái/phải sẽ làm OKS tăng đáng kể.
**TODO:** tự bổ sung nhận xét ảnh này dễ hay khó dựa trên quan sát ảnh thật. Nếu đây là ảnh dễ, có thể giải thích rằng lỗi xảy ra do thao tác nhanh và xác định trái/phải theo phía màn hình thay vì theo cơ thể người.

## 3. Kiểm chéo

Bạn cùng nhóm: **TODO**

| Khớp     | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| -------- | --: | -: | ---: | ------------------------------------ |
| **TODO** |     |    |      |                                      |
| **TODO** |     |    |      |                                      |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

* **TODO – cần dữ liệu từ phần kiểm chéo với bạn cùng nhóm.**

## 4. Model

Phần này điền sau khi chạy `notebooks/day4_pose_finetune_yolo26.ipynb` và có `outputs/eval_model.json`.

| Chỉ số         | yolo26n-pose gốc | Sau fine-tune |    Chênh |
| -------------- | ---------------: | ------------: | -------: |
| pose_mAP50     |         **TODO** |      **TODO** | **TODO** |
| pose_mAP50-95  |         **TODO** |      **TODO** | **TODO** |
| pose_precision |         **TODO** |      **TODO** | **TODO** |
| pose_recall    |         **TODO** |      **TODO** | **TODO** |
| box_mAP50-95   |         **TODO** |      **TODO** | **TODO** |

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu?**
   **TODO – cần `outputs/eval_model.json`.**

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm người dễ hơn hay tìm khớp dễ hơn?**
   **TODO – cần kết quả model.**

3. **Một ảnh test model đoán sai và loại lỗi:**
   **TODO – cần visualization từ notebook.**

4. **Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và dựa vào đâu?**
   **TODO – cần kết quả notebook và kiểm tra ảnh.**

5. **Ảnh bạn gán tệ nhất có cũng là ảnh model đoán tệ nhất không?**
   Ở phần annotation với gold, một trong các skeleton kém nhất của tôi là **`train_02.jpg`, người #1**, với OKS khoảng **0.392** do lỗi đảo trái/phải. Tuy nhiên chưa thể kết luận nó có phải ảnh model đoán tệ nhất hay không cho tới khi chạy đánh giá model.

## 5. Một rule evidence bạn đã dùng

**TODO – cần chọn một trường hợp mà bạn thực sự đã quan sát trên ảnh.**

Có thể viết theo mẫu sau sau khi mở lại một ảnh cụ thể:

`train_XX.jpg`, người #X, keypoint `...`: khớp không nhìn thấy trực tiếp do `...`, nhưng vị trí vẫn có thể suy ra từ `...`. Điểm vẫn nằm trong khung ảnh nên tôi chọn **Occluded (`v=1`)** và đặt điểm ở vị trí ước lượng. Nếu khớp đã nằm ngoài biên ảnh và không còn vị trí hợp lệ trong frame thì tôi chọn **Outside (`v=0`)**.
