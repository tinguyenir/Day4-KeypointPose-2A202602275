# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: **Nguyen Tri Tin** · Hình thức: **Cá nhân** · Ngày: **16/09/2026**

**Phương pháp.** Bộ core gồm 20 ảnh được gán skeleton COCO-17 trong CVAT, export COCO Keypoints và chuyển sang Ultralytics YOLO Pose. Nhãn được kiểm cấu trúc, đếm visibility, xem visualization và đối chiếu protected gold bằng OKS. YOLO26n-pose pretrained trên COCO được đánh giá trước/sau fine-tune trên 10 ảnh test theo notebook. Kết quả model đã được ghi lại dù annotation vẫn còn lỗi đảo trái/phải, nên chưa đáp ứng điều kiện qua cổng gold trước Chặng 6.

Nguồn số liệu và kiểm tra tính nhất quán nằm trong [ARTIFACT_AUDIT.md](ARTIFACT_AUDIT.md). Bảng gold giữ nguyên độ chính xác trong JSON; bảng model trình bày bốn chữ số thập phân như notebook. Không chạy lại evaluator hay huấn luyện trong lần hoàn thiện báo cáo này.

## 1. Nhãn của tôi

Số liệu sau Chặng 4 lấy từ `outputs/visibility_report.json` tại commit khóa nhãn **`eba3e25`**, trước rework. Hai file hiện tại mang tên `visibility_report_before_rework` thực tế chứa số liệu sau rework; không dùng chúng làm baseline.

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 27 |
| v=2 / v=1 / v=0 | 304 / 54 / 101 |
| Thời gian trung bình mỗi ảnh | Không có log thời gian đủ tin cậy để tính chính xác. |

Ba khớp có `%v=1` cao nhất, tính từ số đếm trong JSON tại commit trên:

1. `left_ear`: 6/27 × 100 ≈ **22.22%**.
2. `right_ear`: 5/27 × 100 ≈ **18.52%**.
3. `right_hip`: 5/27 × 100 ≈ **18.52%**, đồng hạng với `right_ear`.

Tỉ lệ này phản ánh cờ đã gán, không phải thước đo trực tiếp của độ khó. Tai có thể bị mũ hoặc góc nhìn che, như [train_06](../dataset/images/train/train_06.jpg); còn tâm khớp hông dưới quần áo cần suy ra từ thân và chân, như [train_03](../dataset/images/train/train_03.jpg). Vì vậy, “thường bị che” khác với “khó xác định vị trí giải phẫu”; lỗi bỏ khớp thành `v=0` cũng làm thống kê chưa phản ánh đầy đủ che khuất thực tế.

Sau rework, [visibility JSON hiện tại](../outputs/visibility_report.json) ghi **20 ảnh, 28 skeleton, v2/v1/v0 = 314/55/107**. Số liệu này được tách khỏi mốc Chặng 4.

## 2. Chấm với gold

Nguồn: [trước rework](../outputs/eval_vs_gold_before_rework.json) và [sau rework](../outputs/eval_vs_gold.json). Số lỗi được đối chiếu với từng finding trong `per_image`.

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.8473 | 0.8454 |
| OKS@0.50 | 0.8966 | 0.931 |
| OKS@0.75 | 0.7931 | 0.8276 |
| Người trong gold | 29 | 29 |
| Người ghép được | 27 | 28 |
| Người thiếu | 2 | 1 |
| Người thừa | 0 | 0 |
| Lỗi `dao_trai_phai` | 1 | 1 |
| Lỗi `nham_nguoi` | 3 | 3 |
| Lỗi `xoa_khop_bi_che` | 16 | 17 |
| Lỗi `thieu_khop` | 9 | 9 |
| Lỗi `thieu_nguoi` | 2 | 1 |
| Lỗi `lech_nhe` | 15 | 15 |

Mean OKS giảm nhẹ **0.0019**, trong khi OKS@0.50 tăng **0.0344**, OKS@0.75 tăng **0.0345** và số người ghép được tăng **27 → 28**. Evaluator tính mean trên các cặp đã ghép, nhưng tính tỉ lệ vượt ngưỡng trên `gold_people + extra_people`; skeleton mới ở `train_13` có OKS **0.7931**, thấp hơn mean trước đó nhưng vượt cả hai ngưỡng. Vì vậy độ bao phủ tăng không đồng nghĩa mean OKS tăng.

**Tôi đã sửa gì giữa hai lần chạy**

Đối chiếu `git diff eba3e25 d5b2e5b -- dataset/labels/train` cho thấy:

- `train_13.jpg`, người #2 trong nhãn sau rework, ghép với gold #2: bổ sung skeleton đủ 17 bộ `(x,y,v)`, trong đó 11 khớp có `v>0`, 6 khớp có `v=0`. Gold #1 vẫn chưa có skeleton tương ứng.
- `train_13.jpg`, người #1, ghép với gold #3: tọa độ y của 12 khớp đã gán giảm khoảng 0.0014 theo tọa độ chuẩn hóa; `right_hip` đổi `v=1 → v=2` và bbox thay đổi nhẹ. OKS của cặp này là **0.9158 → 0.9155**; không có căn cứ gọi đây là cải thiện vị trí.
- `train_03.jpg` và `train_16.jpg`: hai dòng skeleton đổi thứ tự; tọa độ và visibility của mỗi skeleton không đổi. Đây không phải sửa lỗi khớp. Các ảnh khác không đổi nhãn YOLO trong diff này.

Người # trong bảng dưới là `your_person` của JSON sau rework, không phải thứ tự trái sang phải trong ảnh.

| Ảnh / người | Finding còn tồn tại sau rework |
| --- | --- |
| `train_02` / #1 | `dao_trai_phai`; OKS 0.3916; thiếu `left_ear` và còn lệch nhẹ ở chi |
| `train_03` / #1 | `nham_nguoi` tại `right_hip`; bỏ `left_shoulder`, `right_wrist`, `left_hip` bị che thành v=0 |
| `train_06` / #1 | Hai ear: nhãn v=0, gold v=2; `right_hip`: nhãn v=0, gold v=1 |
| `train_08` / #1 | Thiếu `left_hip`; bỏ `left_knee`, `left_ankle` bị che |
| `train_10` / #1 | Bỏ cả hai hip bị che |
| `train_11` / #1 | Bỏ `right_wrist`, `left_hip` bị che |
| `train_13` / #2; gold #1 | #2 bỏ `left_shoulder` bị che; gold #1 vẫn thiếu hẳn |
| `train_15` / #1, #2 | #1 thiếu `right_shoulder`, `right_elbow`, `right_hip`; #2 bỏ `left_knee` bị che và lệch `left_hip` |

Hai finding `nham_nguoi` còn lại là `train_01` / #2 / `right_wrist` và `train_04` / #2 / `left_wrist`. `co_khac_gold` (**45 → 44**) và `gold_khong_gan_nhan` (**14 → 14**) là thông tin chẩn đoán theo rubric, không cộng thành lỗi vị trí.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

[train_02](../dataset/images/train/train_02.jpg), người #1, còn lỗi đảo trái/phải với OKS **0.3916**. Ảnh chỉ có một người nổi bật nên ít nguy cơ nhầm định danh, nhưng người đi xe đạp quay đầu và xoay thân, không phải tư thế nhìn thẳng camera đơn giản. Left/right phải theo cơ thể; trường hợp người quay mặt về camera nói chung dễ làm annotator nhầm với phía màn hình, nhưng artifact không ghi nguyên nhân thao tác cụ thể ở ảnh này. Với `fliplr=0.5` và `flip_idx`, nhãn sai còn được biến đổi sang ảnh lật, nên augmentation không tự khắc phục lỗi ngữ nghĩa trái/phải.

**Giới hạn theo rubric:** mean OKS **0.8454 ≥ 0.75** và OKS@0.75 **0.8276 ≥ 0.70**, nhưng còn **1 lỗi `dao_trai_phai`** nên bài vẫn thuộc diện **Cần rework**. Hoàn thiện văn bản không làm thay đổi chất lượng nhãn.

## 3. Kiểm chéo

Bạn cùng nhóm: **N/A — thực hiện cá nhân**.

Bài được thực hiện cá nhân nên không có partner visibility report để thực hiện phép so sánh peer-to-peer.

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| N/A | N/A | N/A | N/A | Không có artifact của người thứ hai |

Không có luật nào được xác nhận là kết quả thống nhất giữa hai người. Quy ước self-QC trong [GUIDELINE_MINI.md](../GUIDELINE_MINI.md) là: khớp bị che nhưng còn trong frame và suy ra được từ cơ thể liền kề → đặt điểm, `v=1`; khớp ra ngoài frame → `v=0`. [review_partner.md](review_partner.md) ghi limitation và checklist tự kiểm riêng; chúng không thay thế yêu cầu kiểm chéo và so visibility của rubric.

## 4. Model

Nguồn: [eval_model.json](../outputs/eval_model.json), sao chép nguyên byte từ [artifact Colab](../day4_colab_outputs/eval_model.json). Tập test có **10 ảnh, 13 người**.

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

`box_mAP50` là **0.9785 → 0.9600**, chênh **-0.0185**. Pose cải thiện nhẹ ở một số chỉ số, còn box giảm; không đủ căn cứ kết luận model tốt hơn trên mọi mặt.

### Cấu hình và diễn biến huấn luyện

[args.yaml](../day4_colab_outputs/runs/pose_finetune/args.yaml) ghi `yolo26n-pose.pt`, `pretrained=true`, `imgsz=640`, `batch=8`, yêu cầu **80 epoch**, `patience=30`, `seed=20260915`, `fliplr=0.5`, `optimizer=auto`. [Log Colab](../day4_colab_outputs/logs/colab_full_output.log) xác nhận **Tesla T4**, **20 ảnh train / 28 skeleton**. [results.csv](../day4_colab_outputs/runs/pose_finetune/results.csv) có **39 epoch**; bảng môi trường, learning rate và những trường thiếu nằm trong [audit](ARTIFACT_AUDIT.md#thông-tin-huấn-luyện).

Pose mAP50–95 validation đạt **0.68528** ở epoch 1, cao nhất **0.7023** tại epoch **9 và 10**. Box mAP50–95 cao nhất **0.81575** ở epoch **6–8**. Từ epoch 11, pose mAP50–95 giảm xuống **0.00011** ở epoch **11–12**, sau đó phục hồi tới **0.17623** ở epoch **30–31** và kết thúc ở **0.10862**; chưa trở lại mức đầu quá trình.

Train pose loss nhỏ nhất **2.64356** (epoch 10), lớn nhất **8.08082** (epoch 16), cuối cùng **3.68542**; validation pose loss nhỏ nhất **2.02883** (epoch 9–10), lớn nhất **8.4089** (epoch 11–12), cuối cùng **5.45574**. Train box loss giảm từ **2.33202 → 1.4856**, nhưng pose loss không giảm đơn điệu. Diễn biến phù hợp với bất ổn huấn luyện/validation trên bộ train chỉ 20 ảnh; chưa đủ bằng chứng quy nguyên nhân cho overfitting hoặc đo được phương sai giữa nhiều lần chạy.

Notebook đánh giá `best.pt`, không lấy epoch cuối. Việc dừng ở epoch 39, đỉnh đầu tiên tại epoch 9 và `patience=30` phù hợp với early stopping; log thiếu dòng thông báo dừng và số epoch của checkpoint nên chưa xác nhận tuyệt đối “best epoch = 9”. Giá trị **0.7023** trong CSV và **0.6908** ở lần đánh giá cuối là hai phép ghi nhận khác nhau; không thay số này cho số kia khi chưa có bằng chứng giải thích chênh lệch. Theo `data.yaml`, 10 ảnh test cũng là validation để chọn checkpoint, nên đây không phải tập holdout độc lập với quá trình chọn model.

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu?** Tăng **0.6853 → 0.6908**, tức **+0.0055** (0.55 điểm phần trăm). Mức tăng nhỏ trên 10 ảnh; chưa có nhiều seed hoặc khoảng tin cậy để kết luận cải thiện ổn định. Không có bằng chứng tách được kiến thức mới học từ 20 ảnh khỏi kiến thức pretrained COCO.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu?** Sau fine-tune, mAP50–95 của box là **0.8041**, của pose là **0.6908**, chênh **0.1133**. Trên tập test này, kết quả phù hợp với việc định vị vùng người dễ hơn định vị chính xác nhiều keypoint, nhất là chi bị che; không khái quát ra ngoài tập dữ liệu này.

3. **Một ảnh test model đoán sai và loại lỗi:** Trong [prediction test_02](../day4_colab_outputs/runs/predictions/test/test_02.jpg), model đặt một box `person 0.31` cùng skeleton lên con chim trên mép tường phía trái. Đối chiếu [ảnh gốc](../dataset/images/test/test_02.jpg) và [nhãn test](../dataset/labels/test/test_02.txt), đây là lỗi **trượt hẳn**: skeleton không thuộc cơ thể người. Đây là ví dụ sai rõ qua quan sát prediction, không phải kết luận ảnh có OKS model thấp nhất vì không có bảng per-image model-vs-test. Giá trị 0.31 là confidence detection, không phải OKS.

4. **Ảnh nào có OKS model-vs-user thấp nhất? Ai đúng?** Bảng cuối log ghi **`train_02 = 0.337`**, thấp nhất trong các cặp đã ghép. Gold độc lập cũng ghi annotation-vs-gold **0.3916** và `dao_trai_phai`, nên có căn cứ xác định annotation có vấn đề. Điều đó không đồng nghĩa model đúng ở mọi khớp; log không lưu tọa độ prediction train để đối chiếu từng điểm.

5. **Ảnh gán tệ nhất có cũng là ảnh model đoán tệ nhất không?** Trong các skeleton đã ghép với gold, `train_02` thấp nhất (**0.3916**) và cũng có bất đồng model-vs-user thấp nhất (**0.337**). Hai quan sát không đủ để quy kết bản thân ảnh khó: lỗi trái/phải trong nhãn là yếu tố đã được gold phát hiện. Nếu tính cả người bỏ sót, `train_13` còn gold #1 với OKS **0.0**; model phát hiện **3 người**, nhãn có **2**, gold có **3** và evaluator ghi thiếu **1 người**, hỗ trợ kết luận annotation chưa đầy đủ. Sự trùng số lượng không xác nhận model định vị đúng cả ba người.

### Model-vs-label: bất đồng không mặc nhiên là lỗi annotation

| Ảnh | OKS từng cặp trong log (không phải mean theo ảnh) | Đối chiếu độc lập từ gold |
| --- | --- | --- |
| `train_02` | 0.337 | Có lỗi đảo trái/phải |
| `train_01` | 0.679; 0.788 | #2 có finding `right_wrist` nhầm người; log không ghi ID cặp để gán trực tiếp hai OKS này |
| `train_14` | 0.686; 0.887 | OKS với gold 0.9236 và 0.9419; bất đồng model không tự xác nhận nhãn sai |
| `train_15` | 0.739; 0.939 | Gold ghi thiếu khớp và khớp bị che như mục 2 |

| Ảnh | Model / nhãn trong log | Gold / nhãn ghép được | Diễn giải |
| --- | ---: | ---: | --- |
| `train_10` | 2 / 1 | 1 / 1 | Chỉ là bất đồng số lượng, gold không báo thiếu người |
| `train_13` | 3 / 2 | 3 / 2 | Gold xác nhận thiếu 1 người |
| `train_03` | 4 / 2 | 2 / 2 | Chỉ là bất đồng số lượng, không mặc định 4 detection đều đúng |

## 5. Một rule evidence bạn đã dùng

Case [train_11](../dataset/images/train/train_11.jpg), người #1, `right_wrist` được dùng để rà soát quy tắc evidence, không phải một correction đã hoàn thành. Cổ tay phải không thấy rõ trong vùng trước thân gần mặt bàn và con mèo, nhưng vai, khuỷu và hướng cẳng tay phía trái ảnh còn cung cấp căn cứ về vị trí trong frame. Với bằng chứng giải phẫu đó, quyết định theo guideline là đặt điểm ước lượng và dùng **`v=1`**; `v=0` dành cho điểm ngoài frame, không dành cho trường hợp này. Tuy nhiên, [nhãn thực tế](../dataset/labels/train/train_11.txt) vẫn ghi `(0,0,0)` và evaluator báo `xoa_khop_bi_che` (gold `v=1`), nên rule chưa được áp dụng đúng tại khớp này và không được tuyên bố đã sửa.
