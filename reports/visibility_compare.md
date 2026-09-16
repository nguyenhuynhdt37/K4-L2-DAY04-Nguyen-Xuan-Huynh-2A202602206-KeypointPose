# Visibility report

- Thư mục nhãn: `dataset/labels/train`
- 20 ảnh, 29 skeleton, trung bình 14.52 khớp có v > 0 mỗi người
- Tổng: v=2 341 | v=1 80 | v=0 72

So sánh với `friend/coco_keypoints/train` (28 skeleton).
Cột **lệch** là hiệu số phần trăm v=1 - chỗ nào lệch nhiều nhất là chỗ guideline chưa nói rõ.

| # | Khớp | %v=1 (bạn) | %v=1 (đối chiếu) | lệch |
| ---: | --- | ---: | ---: | ---: |
| 13 | left_knee | 31% | 14% | 17 |
| 15 | left_ankle | 28% | 11% | 17 |
| 10 | right_wrist | 28% | 18% | 10 |
| 16 | right_ankle | 28% | 18% | 10 |
| 11 | left_hip | 28% | 21% | 6 |
| 3 | left_ear | 24% | 7% | 17 |
| 14 | right_knee | 24% | 25% | 1 |
| 4 | right_ear | 21% | 7% | 14 |
| 12 | right_hip | 21% | 18% | 3 |
| 8 | right_elbow | 14% | 7% | 7 |
| 9 | left_wrist | 14% | 7% | 7 |
| 5 | left_shoulder | 10% | 11% | 0 |
| 7 | left_elbow | 3% | 7% | 4 |
| 1 | left_eye | 3% | 4% | 0 |
| 0 | nose | 0% | 4% | 4 |
| 2 | right_eye | 0% | 4% | 4 |
| 6 | right_shoulder | 0% | 4% | 4 |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
