# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 28 skeleton, trung bình 13.18 khớp có v > 0 mỗi người
- Tổng: v=2 314 | v=1 55 | v=0 107

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 21 | 1 | 6 | 4% |
| 1 | left_eye | 19 | 2 | 7 | 7% |
| 2 | right_eye | 20 | 1 | 7 | 4% |
| 3 | left_ear | 11 | 6 | 11 | 21% |
| 4 | right_ear | 17 | 5 | 6 | 18% |
| 5 | left_shoulder | 23 | 3 | 2 | 11% |
| 6 | right_shoulder | 27 | 0 | 1 | 0% |
| 7 | left_elbow | 21 | 4 | 3 | 14% |
| 8 | right_elbow | 23 | 2 | 3 | 7% |
| 9 | left_wrist | 19 | 4 | 5 | 14% |
| 10 | right_wrist | 19 | 4 | 5 | 14% |
| 11 | left_hip | 18 | 4 | 6 | 14% |
| 12 | right_hip | 20 | 4 | 4 | 14% |
| 13 | left_knee | 13 | 5 | 10 | 18% |
| 14 | right_knee | 17 | 3 | 8 | 11% |
| 15 | left_ankle | 13 | 3 | 12 | 11% |
| 16 | right_ankle | 13 | 4 | 11 | 14% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
