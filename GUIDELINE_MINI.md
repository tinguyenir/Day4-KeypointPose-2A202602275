# Mini guideline - thực hiện cá nhân

Người gán: **Nguyen Tri Tin** · Ngày: **16/09/2026**

Quy ước annotation và các điểm cần rà soát được ghi cho bài làm của một người. Các quyết định đề xuất dưới đây không đồng nghĩa nhãn hiện tại đã được sửa theo quy ước; trạng thái thực tế được nêu riêng cho từng case.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

* Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
* Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
* Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
* Bị che, còn trong khung → `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
* Ra ngoài mép ảnh → `v = 0`, **không** đặt chấm.
* Không dùng `Hidden` (`h`) vì trạng thái này không được lưu đúng vào file export.

## 2. Quy ước annotation sử dụng trong bài

| Tình huống | Điều kiện / bằng chứng → quyết định | Căn cứ kiểm tra |
| --- | --- | --- |
| Hip dưới quần áo | Hông còn trong frame; trục thân và hướng đùi cho phép suy ra tâm khớp → đặt tọa độ giải phẫu, `v=1`. Hông ngoài frame → `v=0`. Không đặt theo mép áo hoặc túi quần. | Đối chiếu vai–thân–hai đùi của cùng người. |
| Ear bị tóc hoặc mũ che | Tâm tai thấy rõ → `v=2`. Tai bị che, còn trong frame và suy ra được từ hướng đầu, vùng hàm/cổ và đường viền đầu → đặt điểm, `v=1`. Tai ngoài frame → `v=0`. | Không dùng vùng tóc/mũ như tọa độ tai chỉ vì gần đầu; cần xác định cả bên cơ thể. |
| Người bị crop | Xét riêng từng khớp: ngoài mép ảnh → `v=0`; còn trong frame nhưng bị che và có căn cứ từ đoạn chi liền kề → `v=1`; thấy rõ → `v=2`. | Giữ đủ 17 bộ giá trị; không kéo điểm ngoài ảnh vào sát biên để tạo tọa độ giả. |
| Wrist sau tay lái hoặc thân người | Vai, khuỷu và hướng cẳng tay cùng vị trí vật che cho phép suy ra cổ tay trong frame → đặt điểm, `v=1`. Cổ tay ngoài frame → `v=0`. | Lần theo đúng cẳng tay, không lấy tay lái hoặc bàn tay người khác làm tâm khớp. |
| Hai người overlap | Xác định người đang gán, hoàn thành từng skeleton; khớp của người đó bị người khác che nhưng còn suy ra được → `v=1`. Khớp thực sự ngoài frame → `v=0`. | Theo chuỗi vai–khuỷu–cổ tay và hông–gối–cổ chân; màu/đường nối chỉ là tín hiệu cần kiểm tra. |
| Người nhỏ | Trong bộ core, không tự đặt ngưỡng kích thước để bỏ người. Tạo skeleton đủ 17 điểm; khớp bị che trong frame có căn cứ → `v=1`, khớp ngoài frame → `v=0`. | Phân biệt người thật với vật thể và ghi rõ khớp chưa đủ căn cứ; không bỏ cả skeleton vì khó thấy mặt. |

**Khi mất hoàn toàn bằng chứng:** bước 5 của `lab-guide.html` có mô tả Outside cho trường hợp này, trong khi luật bắt buộc và rubric giới hạn `v=0` ở khớp ngoài frame. Vì vậy giữ nguyên contract mục 1, ghi case chưa quyết định để đối chiếu hướng dẫn lớp; không tự coi “khó nhìn” là bằng chứng cho `v=0`, cũng không tự đặt tọa độ thiếu căn cứ. Khác biệt diễn đạt này không hợp thức hóa những khớp evaluator đã xác nhận bị bỏ sót.

### Ảnh mẫu và phạm vi bằng chứng

**Screenshot chưa được lưu trong repository.** Cụ thể, chưa có screenshot CVAT của các quyết định annotation trong bài cá nhân. Các ảnh trong [assets/guide/cvat](assets/guide/cvat/) là ảnh hướng dẫn phát sẵn, không phải bằng chứng thao tác của sinh viên.

| Quy ước | Ảnh thật để đối chiếu | Phạm vi sử dụng |
| --- | --- | --- |
| Hip / overlap | [train_03](dataset/images/train/train_03.jpg) | Người phía sau bị người phía trước che; `right_hip` của nhãn #1 có finding nhầm người. |
| Ear | [train_06](dataset/images/train/train_06.jpg) | Người đội mũ bảo hiểm, nhìn từ phía sau; không khẳng định tai nhìn rõ chỉ vì gold dùng v=2. |
| Crop | [train_13](dataset/images/train/train_13.jpg) | Chân người mặc vest phía trước bị cắt ở mép dưới; xử lý khớp theo từng vị trí, không suy rộng cho người phía sau. |
| Wrist | [train_11](dataset/images/train/train_11.jpg) | Cổ tay ở vùng trước thân gần mặt bàn/con mèo khó quan sát; evaluator xác nhận `right_wrist` bị bỏ. |
| Người nhỏ | [train_13](dataset/images/train/train_13.jpg) | Người nhỏ ở nền bên trái là tình huống cần rà soát bao phủ; gold vẫn báo thiếu một người. Không tuyên bố người này đã được bổ sung. |

Các visualization đã có tại `outputs/vis_train/` được xem cùng ảnh gốc. Thư mục này bị Git ignore, không phải screenshot CVAT và không được dùng làm bằng chứng nhãn đã sửa. Lần audit còn vẽ lại nhãn hiện tại bằng `visualize_pose.py` vào thư mục tạm để kiểm tra, không ghi đè ảnh có sẵn.

## 3. Ba ca mơ hồ đã gặp

Các case dưới đây được evaluator phát hiện là điểm guideline cần xem lại. Người # là `your_person` trong [eval_vs_gold.json](outputs/eval_vs_gold.json) sau rework; không phải thứ tự trái sang phải.

### Ca 1 — train_06, người #1, left_ear / right_ear

- **Ảnh và nhãn:** [ảnh train_06](dataset/images/train/train_06.jpg), [nhãn](dataset/labels/train/train_06.txt).
- **Mơ hồ:** mũ bảo hiểm và góc nhìn phía sau làm tâm tai khó xác định; che khuất dễ bị nhầm với Outside.
- **Trạng thái thực tế:** cả hai tai vẫn là `(0,0,0)`. Evaluator ghi `thieu_khop` cho cả hai, gold `v=2`; không có thay đổi nhãn ảnh này trong diff rework.
- **Quyết định khi rà soát:** không giữ `v=0` chỉ vì tai bị mũ che. Nếu xác định được vị trí tai trong frame từ hướng đầu và vùng hàm/cổ thì đặt điểm, `v=1`; nếu tâm tai thật sự thấy rõ thì `v=2`. Chưa có correction hoặc tọa độ mới được ghi.
- **Bằng chứng:** đầu và mũ nằm trong frame; ảnh gốc cung cấp hướng đầu, còn gold cung cấp finding độc lập rằng các tai đang bị bỏ. Gold v=2 không được thay thế cho quan sát để khẳng định mức nhìn thấy.
- **Nếu quyết định ngược:** gán v=0 cho tai bị che nhưng định vị được sẽ loại supervision tọa độ ở tai; ngược lại, đặt điểm tùy ý trên mũ sẽ tạo mục tiêu vị trí sai.

### Ca 2 — train_03, người #1, right_hip

- **Ảnh và nhãn:** [ảnh train_03](dataset/images/train/train_03.jpg), [nhãn](dataset/labels/train/train_03.txt). Nhãn #1 là người phía sau, mặt gần giữa ảnh; người mặc áo khoác da và mũ đứng phía trước.
- **Mơ hồ:** hai thân người overlap; tâm hông dưới quần áo dễ bị đặt theo vùng cơ thể gần nhất thay vì đúng người.
- **Trạng thái thực tế:** `right_hip=(0.559649,0.392516,1)`; evaluator vẫn báo `nham_nguoi`. Diff rework chỉ đổi thứ tự hai skeleton trong ảnh này.
- **Quyết định khi rà soát:** xác định lại trục thân và đùi của người phía sau; nếu hông bị che nhưng còn trong frame thì giữ v=1 và chỉ định vị bằng căn cứ của người đó. Chưa có tọa độ điều chỉnh được xác nhận.
- **Bằng chứng:** ảnh cho thấy overlap; finding cho biết điểm hiện tại gần right_hip của người khác hơn so với khớp gold tương ứng. Cờ v=1 đúng loại tình huống không tự bảo đảm tọa độ đúng.
- **Nếu quyết định ngược:** lấy hông người phía trước làm mục tiêu cho người phía sau có thể dạy model nối khớp giữa hai định danh.

### Ca 3 — train_11, người #1, right_wrist

- **Ảnh và nhãn:** [ảnh train_11](dataset/images/train/train_11.jpg), [nhãn](dataset/labels/train/train_11.txt).
- **Mơ hồ:** vùng cổ tay trước thân gần mặt bàn/con mèo bị che; nhìn thấy một phần tay nhưng khó xác định chính xác tâm cổ tay.
- **Trạng thái thực tế:** `right_wrist=(0,0,0)`; evaluator ghi `xoa_khop_bi_che`, gold v=1. Không có correction tại ảnh này trong history rework.
- **Quyết định khi rà soát:** theo vai và khuỷu phải phía trái ảnh để suy ra đoạn cẳng tay; còn căn cứ cho vị trí trong frame → đặt điểm ước lượng, v=1. Đây là đề xuất xử lý, chưa được áp dụng vào nhãn.
- **Bằng chứng:** vai/khuỷu còn thấy trong ảnh, vùng che nằm trong frame và evaluator độc lập xác nhận khớp bị che.
- **Nếu quyết định ngược:** dùng v=0 sẽ làm mất supervision tọa độ cho cổ tay bị che; dùng v=2 sẽ mô tả sai mức quan sát.

## 4. Sau khi so visibility report với bạn cùng nhóm

Bài thực hiện cá nhân; không có partner artifact để so sánh.

| Nội dung | Kết quả |
| --- | --- |
| Người cùng nhóm / reviewer độc lập | N/A |
| Khớp lệch %v=1 lớn nhất giữa hai người | N/A |
| %v=1 của partner / độ lệch | N/A |
| Nguyên nhân bất đồng giữa hai người | N/A |
| Luật bổ sung sau khi hai bên thống nhất | N/A |

Các quy ước tại mục 2 được làm rõ qua self-QC và gold, không phải peer review. Yêu cầu so visibility và kiểm chéo của rubric chưa được hoàn thành; xem [review_partner.md](reports/review_partner.md).
