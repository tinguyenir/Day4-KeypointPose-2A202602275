# Ghi chú kiểm chéo — bài thực hiện cá nhân

Người gán: **Nguyen Tri Tin** · Reviewer độc lập: **N/A** · Ngày: **16/09/2026**

Bài được thực hiện cá nhân nên không có partner visibility report hoặc bộ nhãn của người thứ hai để review. Không có tên reviewer, nhận xét peer-to-peer hay số liệu so sánh giữa hai người được xác nhận. Tệp này ghi limitation và kết quả **self-QC**, không thay thế yêu cầu kiểm chéo 7 điểm và phần so visibility trong tiêu chí 10 điểm của [RUBRIC.md](../RUBRIC.md).

## Phạm vi self-QC

- `check_pose_labels.py`: đã chạy trên 20 file nhãn train; **28 skeleton, 0 lỗi định dạng, 16 cảnh báo visibility**, exit code 0.
- `visibility_report.py`: dùng hàm `collect` để đếm lại trong bộ nhớ và đối chiếu JSON; dùng `render_markdown` để kiểm tra Markdown hiện tại. Cả hai khớp dữ liệu, **v2/v1/v0 = 314/55/107**; không ghi đè report đã sinh.
- `visualize_pose.py`: đã vẽ lại 28 skeleton vào thư mục tạm bằng Python trong `.venv`; xem các case `train_02`, `train_03`, `train_06`, `train_11` cùng ảnh gốc và visualization đã lưu. Đây là tự kiểm chọn mẫu, không phải xác nhận mọi khớp đều đúng.
- Gold OKS evaluator: đọc đầy đủ các kết quả trước/sau trong JSON đã lưu và đối chiếu cách tính trong script. Không chạy lại evaluator, không thay đổi gold hoặc nhãn.

## Reviewer checklist: trạng thái peer review và đối chiếu tự kiểm

Đối chiếu 11 mục của [REVIEWER_CHECKLIST.md](REVIEWER_CHECKLIST.md). Cột peer review đều N/A vì không có người kiểm độc lập; các kết quả tự kiểm được ghi riêng, không đánh dấu giả là đã được partner duyệt.

| # | Mục kiểm | Peer review | Kết quả self-QC / giới hạn |
| ---: | --- | --- | --- |
| 1 | Mọi người đều đủ 17 điểm, không thiếu người | N/A | 28 skeleton đều đủ 17 bộ giá trị; chưa đạt bao phủ vì gold có 29 người, còn thiếu 1 tại train_13. |
| 2 | Không có dấu hiệu đảo trái/phải ở vai/hông | N/A | Chưa đạt: train_02 #1 còn `dao_trai_phai`, OKS 0.3916. |
| 3 | Không nối sang cơ thể khác | N/A | Chưa đạt: 3 finding `nham_nguoi` tại train_01, train_03, train_04. |
| 4 | Khớp bị che dùng v=1 và có chấm | N/A | Chưa đạt: 17 finding `xoa_khop_bi_che`. |
| 5 | v=0 chỉ ở khớp ngoài frame | N/A | Chưa đạt: các khớp bị che trong frame vẫn bị bỏ; check cấu trúc có 16 cảnh báo cần xem lại. |
| 6 | Không có dấu hiệu dùng Hidden | N/A | Không có log thao tác CVAT để xác nhận; kiểm chọn mẫu không đủ kết luận toàn bộ bài. |
| 7 | COCO Keypoints, 51 số/người | N/A | PASS cấu trúc: 20 ảnh, 28 annotation, mỗi mảng keypoints có 51 số; chuyển đổi trong bộ nhớ khớp nhãn YOLO. |
| 8 | YOLO Pose, 56 số/dòng, kpt_shape [17,3] | N/A | PASS cấu trúc trên toàn bộ 20 file train. |
| 9 | Nộp visibility report và đặt hai bảng cạnh nhau | N/A | Report cá nhân có và khớp nhãn; MISSING phép so sánh với partner. |
| 10 | Ghi các ca chưa rõ vào mini guideline | N/A | Có 3 case có nguồn và lý do; không xác nhận đã ghi hết mọi ca mơ hồ. |
| 11 | check_pose_labels.py chạy 0 lỗi | N/A | PASS định dạng, exit code 0; vẫn có 16 cảnh báo. |

## Finding tự kiểm — không phải lỗi tìm trong bài người khác

Người # là `your_person` trong JSON sau rework, trừ trường hợp ghi rõ gold #. Cột cuối là hướng xử lý theo guideline, **chưa được thực hiện trong lần hoàn thiện văn bản này**.

| Ảnh | Người thứ | Khớp | Lỗi gì | Hướng sửa / kiểm lại |
| --- | --- | --- | --- | --- |
| train_02 | Nhãn #1 | Các cặp trái/phải | `dao_trai_phai` | Xác định bên cơ thể từ tư thế thật, kiểm từng cặp trong CVAT; không chỉ dùng phía màn hình. |
| train_01 | Nhãn #2 | right_wrist | `nham_nguoi` | Lần theo khuỷu và cẳng tay của đúng người để định vị lại. |
| train_03 | Nhãn #1 | right_hip | `nham_nguoi` | Dựa vào thân và chân người phía sau; v=1 không bảo đảm tọa độ đã đúng. |
| train_04 | Nhãn #2 | left_wrist | `nham_nguoi` | Kiểm định danh theo chuỗi vai–khuỷu–cổ tay của skeleton tương ứng. |
| train_06 | Nhãn #1 | left_ear / right_ear | `thieu_khop`, nhãn v=0, gold v=2 | Kiểm vị trí tai trong frame; chỉ định v=1/v=2 theo mức quan sát, không mặc định Outside vì mũ che. |
| train_11 | Nhãn #1 | right_wrist | `xoa_khop_bi_che`, gold v=1 | Dùng hướng cẳng tay và vùng che để xác định điểm trong frame; theo guideline đặt điểm v=1. |
| train_13 | Gold #1; chưa có nhãn tương ứng | Toàn skeleton | `thieu_nguoi` | Rà lại từng người trong ảnh và bổ sung đủ 17 bộ giá trị nếu mở vòng annotation mới được phép. |

Trong các lỗi có phân loại (không tính hai mục chẩn đoán cờ), lỗi lặp nhiều nhất là `xoa_khop_bi_che`: **17 finding**. Quan sát phù hợp với việc quy tắc che khuất chưa được áp dụng nhất quán; không có log thao tác để tách chắc chắn lỗi sử dụng CVAT khỏi cách hiểu guideline. Kết quả và các giới hạn còn lại được tổng hợp trong [REPORT.md](REPORT.md) và [ARTIFACT_AUDIT.md](ARTIFACT_AUDIT.md).
