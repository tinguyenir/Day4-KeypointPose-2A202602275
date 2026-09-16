# Mini guideline - nhóm: **TODO** | người gán: **Nguyen Tri Tin** | ngày: **16/09/2026**

> File này ghi lại các quyết định khi gán nhãn để giữ cách xử lý nhất quán giữa các ảnh và giữa các thành viên trong nhóm.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

* Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
* Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
* Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
* Bị che, còn trong khung → `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
* Ra ngoài mép ảnh → `v = 0`, **không** đặt chấm.
* Không dùng `Hidden` (`h`) vì trạng thái này không được lưu đúng vào file export.

## 2. Luật của nhóm bạn

| Tình huống                                       | Luật nhóm bạn chọn                                                                                                                                                                                                            | Vì sao                                                                                                                                       |
| ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Hông của người mặc quần áo dài                   | Nếu hông vẫn nằm trong khung nhưng không nhìn thấy trực tiếp, đặt `left_hip` và `right_hip` tại vị trí giải phẫu ước lượng theo vai, thân người và hướng hai chân; chọn `v=1`. Chỉ dùng `v=0` khi hông thực sự nằm ngoài ảnh. | Tâm khớp hông thường không có bề mặt nhìn thấy rõ qua quần áo. Nếu bỏ hẳn thành `v=0` sẽ làm mất thông tin pose dù vị trí vẫn có thể suy ra. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần         | Nếu còn thấy một phần tai hoặc có thể suy ra vị trí từ mắt, đầu và đường viền khuôn mặt thì vẫn đặt điểm và chọn `v=1`. Nếu tai nhìn thấy rõ thì `v=2`.                                                                       | Che khuất không đồng nghĩa với ngoài khung. Tai vẫn thuộc pose của người và có thể định vị tương đối từ cấu trúc đầu.                        |
| Người bị cắt ở mép ảnh, chỉ thấy từ hông trở lên | Các keypoint còn trong ảnh được gán bình thường. Các keypoint chân đã nằm ngoài mép ảnh chọn `v=0`; không đặt điểm giả ở sát biên.                                                                                            | `v=0` dùng cho keypoint thực sự nằm ngoài frame. Việc kéo điểm vào mép ảnh sẽ tạo tọa độ sai cho model.                                      |
| Cổ tay nằm sau tay lái / sau thân mình           | Nếu hướng cẳng tay cho phép suy ra vị trí cổ tay và cổ tay vẫn nằm trong frame thì đặt tại vị trí ước lượng và chọn `v=1`. Nếu nhìn rõ cổ tay thì `v=2`.                                                                      | Vật thể che cổ tay là occlusion, không phải Outside. Hướng elbow–wrist cung cấp bằng chứng hình học để ước lượng.                            |
| Hai người chồng lên nhau                         | Hoàn thành toàn bộ skeleton của từng người trước khi chuyển sang người kế tiếp. Mỗi point phải nằm trên đúng cơ thể mà skeleton đang thuộc về. Điểm bị người khác che nhưng vẫn suy ra được chọn `v=1`.                       | Tránh lỗi `nham_nguoi`, đặc biệt ở wrist và hip khi hai người đứng gần hoặc chồng lên nhau.                                                  |
| Người nhỏ đến mức nào thì không gán nữa          | Với bộ core của lab này, không tự loại người chỉ vì nhỏ. Nếu vẫn nhận ra đó là một người trong ảnh thì tạo một skeleton đủ 17 keypoint; các điểm không quan sát được xử lý bằng `v=1` hoặc `v=0` theo bằng chứng.             | GUIDE cho biết bộ ảnh đã được chọn để mọi người đều đủ lớn để gán. Tự đặt ngưỡng kích thước sẽ làm thiếu skeleton.                           |

### Ảnh mẫu cho từng luật

1. **Hông bị quần áo che:**
   `[Chèn screenshot CVAT của một ảnh có hip được đặt v=1, ví dụ train_11 hoặc train_10]`

2. **Tai bị che:**
   `[Chèn screenshot CVAT của một trường hợp left_ear/right_ear bị che, ví dụ train_06]`

3. **Người bị cắt ở mép ảnh:**
   `[Chèn screenshot CVAT của ảnh có chân hoặc một phần cơ thể ra ngoài frame]`

4. **Cổ tay bị che:**
   `[Chèn screenshot CVAT của trường hợp right_wrist bị che, ví dụ train_11]`

5. **Hai người chồng lên nhau:**
   `[Chèn screenshot CVAT của ảnh nhiều người, ví dụ train_03]`

6. **Người nhỏ:**
   `[Chèn screenshot CVAT của người nhỏ nhất mà bạn vẫn tạo đủ skeleton]`

## 3. Ba ca mơ hồ đã gặp

### Ca 1 - ảnh `train_06.jpg`, người thứ `1`, khớp `left_ear / right_ear`

* **Mơ hồ ở chỗ nào:** Hai tai khó nhìn rõ và ban đầu tôi không chắc nên đánh dấu Outside hay Occluded.
* **Bạn quyết thế nào:** Nếu tai vẫn nằm trong vùng đầu và trong khung ảnh nhưng bị góc nhìn hoặc phần đầu che, tôi đặt point ở vị trí ước lượng và chọn `v=1`.
* **Vì sao:** Có thể suy ra vị trí tai từ mắt, hướng khuôn mặt và hình dạng đầu. Tai không thực sự nằm ngoài frame.
* **Nếu người khác quyết ngược lại thì model học sai cái gì:** Nếu dùng `v=0`, model sẽ không được học vị trí tai trong các trường hợp occlusion và có thể coi các keypoint bị che như không tồn tại.

### Ca 2 - ảnh `train_03.jpg`, người thứ `1`, khớp `right_hip`

* **Mơ hồ ở chỗ nào:** Trong ảnh có nhiều người đứng gần nhau nên point ở vùng hông dễ bị kéo sang cơ thể người bên cạnh.
* **Bạn quyết thế nào:** Xác định skeleton đang thuộc về người nào trước, sau đó đặt `right_hip` theo trục thân và chân của đúng người đó; không chọn point chỉ vì nó gần vị trí hông nhất trên màn hình.
* **Vì sao:** Left/right và identity của point phải theo cơ thể người đang được gán, không theo vị trí hình học của người bên cạnh.
* **Nếu người khác quyết ngược lại thì model học sai cái gì:** Model có thể học nối một phần skeleton của người này sang người khác, gây lỗi `nham_nguoi` trong ảnh đông người.

### Ca 3 - ảnh `train_11.jpg`, người thứ `1`, khớp `right_wrist`

* **Mơ hồ ở chỗ nào:** Cổ tay không nhìn thấy rõ nên khó quyết định giữa `v=1` và `v=0`.
* **Bạn quyết thế nào:** Vì cổ tay vẫn nằm trong frame và vị trí có thể suy ra từ hướng cẳng tay/elbow, tôi dùng `v=1` và đặt chấm ước lượng.
* **Vì sao:** Đây là trường hợp khớp bị che chứ không phải khớp nằm ngoài ảnh.
* **Nếu người khác quyết ngược lại thì model học sai cái gì:** Nếu đặt `v=0`, model mất supervision cho các wrist bị che và có thể giảm khả năng dự đoán pose khi tay bị vật hoặc cơ thể che khuất.

## 4. Sau khi so visibility report với bạn cùng nhóm

* Khớp lệch `%v=1` nhiều nhất: **TODO**
  (bạn `TODO%` / họ `TODO%`)

* Nguyên nhân là **TODO: guideline chưa rõ / một trong hai bên gán sai**

* Luật mới bổ sung vào mục 2 sau khi thống nhất:

  **TODO – chỉ điền sau khi có visibility report của bạn cùng nhóm.**

Ví dụ cách viết sau khi có số liệu:

> Với các keypoint hông vẫn nằm trong frame nhưng bị quần áo che, hai bên thống nhất luôn đặt điểm theo vị trí giải phẫu ước lượng và sử dụng `v=1`; chỉ dùng `v=0` khi vị trí khớp thực sự nằm ngoài mép ảnh.
