# Audit artifact — Day 4 Keypoint & Pose

Ngày kiểm tra: **16/09/2026**. Phạm vi: hoàn thiện văn bản trên branch `main`, dựa vào file trong workspace và Git history. Không truy nguồn dataset bên ngoài, không sửa nhãn, gold, test, metric cũ, notebook hoặc weights.

## Nguồn và mốc dữ liệu

| Nội dung | Nguồn dùng trong báo cáo | Lưu ý |
| --- | --- | --- |
| Yêu cầu | [README](../README.md), [GUIDE](../GUIDE.md), [RUBRIC](../RUBRIC.md), [lab-guide](../lab-guide.html), [template](REPORT_TEMPLATE.md), [checklist](REVIEWER_CHECKLIST.md) | Rubric còn yêu cầu hết lỗi trái/phải và có review của người khác. |
| Baseline sau Chặng 4 | `eba3e25:outputs/visibility_report.json` và `eba3e25:reports/visibility_report.md` | 20 ảnh, 27 skeleton, v2/v1/v0 = 304/54/101. |
| Visibility hiện tại | [JSON](../outputs/visibility_report.json), [Markdown](visibility_report.md) | 20 ảnh, 28 skeleton, 314/55/107; đã kiểm lại bằng script trong bộ nhớ. |
| Gold trước/sau | [before](../outputs/eval_vs_gold_before_rework.json), [after](../outputs/eval_vs_gold.json) | Dùng các giá trị JSON đầy đủ, không lấy số hiển thị ba chữ số trên console. |
| Rework thực tế | `git diff eba3e25 d5b2e5b -- dataset/labels/train` | train_13 bổ sung người và chỉnh nhẹ skeleton cũ; train_03/train_16 đổi thứ tự dòng. |
| Export / nhãn | [COCO JSON](../annotations/coco_keypoints/person_keypoints_default.json), `dataset/labels/train/*.txt`, `dataset/labels/test/*.txt` | Đã đọc đủ 20 file train và 10 file test; không sửa. |
| Model | [eval_model.json](../outputs/eval_model.json), [bản Colab](../day4_colab_outputs/eval_model.json) | File ở outputs ban đầu thiếu và không có trong Git history; sao chép nguyên byte artifact sẵn có, không sinh kết quả mới. |
| Training | [args.yaml](../day4_colab_outputs/runs/pose_finetune/args.yaml), [results.csv](../day4_colab_outputs/runs/pose_finetune/results.csv), [log](../day4_colab_outputs/logs/colab_full_output.log) | CSV có 39 dòng epoch; log có đoạn lặp, không coi là bằng chứng nhiều thí nghiệm độc lập. |
| Prediction | [test_02](../day4_colab_outputs/runs/predictions/test/test_02.jpg) và 9 ảnh cùng thư mục; ảnh batch trong `runs/pose_eval/` | Đã xem ảnh thật để chọn lỗi trượt hẳn trên con chim; không tạo prediction mới. |
| Ghi chú bổ sung | `a.md` | File có 0 byte tại thời điểm kiểm tra; không chứa số liệu. |

**Sai lệch tên snapshot:** `outputs/visibility_report_before_rework.json` có nội dung giống file visibility hiện tại; `reports/visibility_report_before_rework.md` cũng ghi 28 skeleton. Các bản tương ứng trong gói Colab có cùng dữ liệu. Đây không phải baseline 27 skeleton, nên báo cáo truy về commit `eba3e25`, không tự sửa các snapshot đã lưu.

Các JSON gold và visibility trong `day4_colab_outputs/` có nội dung JSON bằng các bản tương ứng ở `outputs/` dù byte/line ending khác nhau. Notebook trong repo không chứa output cell đã chạy; kết quả chạy được lấy từ log và artifact Colab, không gán cho notebook một output không tồn tại.

Lệnh truy vết chỉ đọc:

```powershell
git show eba3e25:outputs/visibility_report.json
git show eba3e25:reports/visibility_report.md
git diff eba3e25 d5b2e5b -- dataset/labels/train
```

## Thông tin huấn luyện

“Không có trong artifact đã ghi” nghĩa là chưa xác minh được từ lần chạy này; không lấy thông số dự kiến hoặc môi trường Python local thay thế môi trường Colab.

| Trường | Giá trị / mức xác minh | Nguồn |
| --- | --- | --- |
| Model | YOLO26n-pose, `yolo26n-pose.pt` | Notebook, args.yaml |
| Ultralytics version | **8.4.153**, từ metadata checkpoint; log không ghi version | Đọc token metadata `version` trong `weights/best.pt` và `last.pt` bằng `zipfile`/`pickletools`, không load model hoặc thực thi pickle |
| Python version trên Colab | Không có trong artifact đã ghi | Notebook không có output môi trường; log không ghi version |
| PyTorch version | Không có trong artifact đã ghi | Như trên |
| CUDA | Dùng GPU/device 0; version CUDA và driver không có trong artifact đã ghi | Log ghi `device : 0`, args.yaml ghi device '0' |
| GPU | Tesla T4 | Log |
| VRAM | Tổng dung lượng không có trong artifact đã ghi; cột bộ nhớ GPU trong progress ghi khoảng 1.48G–1.6G | Log; đây là bộ nhớ được progress báo, không phải tổng VRAM |
| Train images / instances | 20 / 28 | Log và kiểm nhãn |
| Test images / instances | 10 / 13 | Log và 10 file nhãn test |
| imgsz / batch | 640 / 8 | args.yaml |
| Optimizer | Cấu hình `auto`; không có dòng xác nhận optimizer được chọn nên chưa xác minh AdamW | args.yaml, log |
| Learning rate cấu hình | `lr0=0.01`, `lrf=0.01`; không mặc định đây là LR mà optimizer auto thực sự dùng | args.yaml |
| Learning rate ghi trong CSV | Cả pg0/pg1/pg2: epoch 1 = 0.000444444; lớn nhất 0.00192575 tại epoch 4; epoch 39 = 0.0010595 | results.csv |
| Epochs requested / completed | 80 / 39 | args.yaml / CSV có epoch 1–39, phù hợp progress log |
| Best epoch | Đỉnh pose mAP50–95 đầu tiên: 9, đồng hạng 10; epoch chính xác của checkpoint chưa được xác nhận trực tiếp | CSV; checkpoint đã strip có `epoch=-1`, không dùng -1 như epoch huấn luyện |
| Checkpoint đánh giá | `pose_finetune/weights/best.pt` | Log và cell đánh giá trong notebook |
| Early stopping patience | 30 | args.yaml; dừng 39 sau đỉnh đầu tiên 9 phù hợp early stopping, nhưng thiếu dòng thông báo dừng trong log |
| Seed / deterministic | 20260915 / true | args.yaml |
| fliplr / pretrained | 0.5 / true | args.yaml |
| Inference speed | Không có trong artifact đã ghi | Không dùng tốc độ vòng validation hoặc thời gian progress thay latency inference |
| Parameter count / GFLOPs | Không có trong log/notebook/CSV đã ghi | Không suy từ tên model hoặc kích thước file .pt |

Metadata `train_metrics` của best.pt khớp giá trị validation tại epoch 9–10 (ví dụ pose mAP50–95 0.7023), nhưng không phân biệt được hai epoch. `train_results` trong checkpoint đã chứa lịch sử đến 39 nên cũng không đủ để gán epoch lưu best. CSV và lần đánh giá cuối ghi pose mAP50–95 khác nhau (**0.7023 / 0.6908**); nguyên nhân không được log xác nhận.

Checkpoint chỉ được đọc để kiểm metadata, không đưa vào danh sách file cần git add. SHA-256 tại thời điểm audit:

| File | SHA-256 |
| --- | --- |
| `day4_colab_outputs/runs/pose_finetune/weights/best.pt` | `b48d34c38c6013ce3d814d51db6fff642bce09b0e1694f85698284af7cee34de` |
| `day4_colab_outputs/runs/pose_finetune/weights/last.pt` | `0f61bcd9a7992079a94957bf66726705c975091a01f73a53f073a9a3433adba8` |
| `outputs/eval_model.json` và bản nguồn Colab | `6b5940c827e6b1673518379078d6ebd20ba7bbb50be30ff5ce07cfa197054cd8` |

## Kiểm tra tính nhất quán

- `python tools/check_pose_labels.py --images dataset/images/train --labels dataset/labels/train`: **exit 0**, 20/20 file, 28 skeleton, **0 lỗi định dạng / 16 cảnh báo**; không đồng nhất PASS định dạng với đúng nội dung.
- Đếm lại bằng `visibility_report.collect` và dựng Markdown trong bộ nhớ: khớp JSON và Markdown hiện tại; không ghi đè artifact.
- Chuyển COCO sang các dòng YOLO trong bộ nhớ theo đúng logic converter: khớp toàn bộ 20 file train, từng dòng, với **28 mảng keypoints dài 51** và **28 dòng YOLO dài 56**. `data.yaml` có `kpt_shape: [17,3]`.
- Đọc nhãn test: **10 file, 13 skeleton, v2/v1/v0 = 146/29/46**; không sửa nhãn test.
- Python mặc định thiếu Pillow; dùng `.venv/Scripts/python.exe` sẵn có chạy `visualize_pose.py`, vẽ **28 skeleton** vào thư mục tạm ngoài repo. Không cài thêm package, không ghi đè visualization đã có.
- Không chạy lại gold evaluator, không train, không sửa notebook để tạo output. Các nhận định model-vs-label đến từ bảng log đã ghi; bảng đó không lưu ID cặp và tọa độ prediction train.
- So SHA-256 trước/sau trên 168 file nguồn trong dataset, annotations, gold, tools, notebooks, outputs và gói Colab: không file gốc nào thay đổi. Phần luật bắt buộc của mini guideline giữ nguyên; liên kết tương đối trong bốn tài liệu đều tồn tại tại thời điểm kiểm tra. `git diff --check` không phát hiện lỗi whitespace.

## Đối chiếu deliverable và limitation

PASS dưới đây nghĩa là artifact có và nội dung đã được đối chiếu ở phạm vi ghi rõ, không có nghĩa đạt toàn bộ điểm rubric. NOT APPLICABLE mô tả bối cảnh cá nhân, không phải quyết định miễn tiêu chí của giảng viên.

| Deliverable | Trạng thái | Bằng chứng / giới hạn |
| --- | --- | --- |
| `dataset/labels/train/*.txt` | PASS | 20 file hợp lệ; nội dung vẫn còn lỗi gold. |
| `annotations/coco_keypoints/person_keypoints_default.json` | PASS | 20 ảnh, 28 skeleton; khớp YOLO sau chuyển đổi trong bộ nhớ. |
| `reports/visibility_report.md` | PASS | Khớp nhãn hiện tại. |
| `outputs/visibility_report.json` | PASS | Khớp nhãn hiện tại; baseline đọc từ history. |
| `GUIDELINE_MINI.md` | PASS | Giữ contract, 6 tình huống, 3 case có bằng chứng; thiếu screenshot CVAT cá nhân. |
| `outputs/eval_vs_gold.json` | PASS | Kết quả thật có sẵn; rubric vẫn yêu cầu rework vì còn swap. |
| `outputs/eval_model.json` | PASS | Sao chép nguyên byte từ artifact Colab đã có. |
| `reports/REPORT.md` | PASS | Đủ 5 mục template, phân tích bằng số liệu thực tế và nêu giới hạn. |
| `reports/review_partner.md` — ghi chú cá nhân | PASS | Ghi limitation và 11 mục checklist tự kiểm, không giả reviewer. |
| Review partner / bảng so visibility peer-to-peer | NOT APPLICABLE / MISSING theo rubric | Làm cá nhân nên không có partner; yêu cầu rubric chưa được đáp ứng. |
| Screenshot CVAT cho quyết định cá nhân | MISSING | Chỉ có ảnh hướng dẫn phát sẵn; ảnh gốc/visualization không xác nhận thao tác CVAT. |

Các yêu cầu còn chưa đáp ứng: bao phủ đủ 29 người (hiện ghép 28), không đảo trái/phải (còn 1), không nhầm người (còn 3), visibility nhất quán (17 khớp bị che bị bỏ và 9 khớp gold thấy rõ bị bỏ), kiểm chéo và bảng so visibility của người khác. Không tự chấm điểm hoặc xác nhận miễn peer review. Ngoài ra còn thiếu timing annotation, một số thông số môi trường và bằng chứng trực tiếp về epoch checkpoint/optimizer; các mục này được ghi thiếu thay vì suy đoán.

Gói `day4_colab_outputs/` có sẵn nhưng chưa được Git theo dõi lúc bắt đầu. Để người đọc bản nộp kiểm lại được phân tích, cần thêm riêng các artifact được dẫn: `eval_model.json`, `logs/colab_full_output.log`, `runs/pose_finetune/args.yaml`, `runs/pose_finetune/results.csv` và `runs/predictions/test/test_02.jpg`. Không thêm cả thư mục, không thêm weights; `a.md` rỗng không phải deliverable.
