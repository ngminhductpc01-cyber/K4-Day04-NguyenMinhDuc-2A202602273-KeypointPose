# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 28 skeleton, trung bình 15.75 khớp có v > 0 mỗi người
- Tổng: v=2 298 | v=1 143 | v=0 35

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 22 | 6 | 0 | 21% |
| 1 | left_eye | 19 | 9 | 0 | 32% |
| 2 | right_eye | 20 | 8 | 0 | 29% |
| 3 | left_ear | 10 | 18 | 0 | 64% |
| 4 | right_ear | 15 | 13 | 0 | 46% |
| 5 | left_shoulder | 24 | 4 | 0 | 14% |
| 6 | right_shoulder | 26 | 2 | 0 | 7% |
| 7 | left_elbow | 22 | 6 | 0 | 21% |
| 8 | right_elbow | 24 | 4 | 0 | 14% |
| 9 | left_wrist | 19 | 9 | 0 | 32% |
| 10 | right_wrist | 17 | 10 | 1 | 36% |
| 11 | left_hip | 17 | 10 | 1 | 36% |
| 12 | right_hip | 16 | 11 | 1 | 39% |
| 13 | left_knee | 13 | 8 | 7 | 29% |
| 14 | right_knee | 12 | 9 | 7 | 32% |
| 15 | left_ankle | 11 | 8 | 9 | 29% |
| 16 | right_ankle | 11 | 8 | 9 | 29% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
