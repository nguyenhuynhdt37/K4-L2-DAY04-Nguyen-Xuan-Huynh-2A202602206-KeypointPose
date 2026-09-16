# Đánh giá chéo bài thực hành Ngày 4 (Peer Review)

Người gán: Phát Đoàn Người kiểm: Nguyễn Xuân Huỳnh   Ngày: 16/09/2026

## 1. Reviewer Checklist


|     | Mục kiểm                                                                | Đạt?      | Ghi chú / ảnh nào                                                                                                 |
| --- | ----------------------------------------------------------------------- | :---------: | ----------------------------------------------------------------------------------------------------------------- |
| 1   | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu                | ☒ Cần sửa | `train_13.jpg` bị thiếu hẳn 2 skeleton người đứng phía sau/bên trái.                                              |
| 2   | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông              | ☒ Cần sửa | `train_02.jpg` (người #1) mắc lỗi đảo trái/phải ở cặp vai và hông.                                                |
| 3   | Không có xương nào kéo dài sang một cơ thể khác                         | ☑ Đạt     | Không có hiện tượng nhầm người ở các bức ảnh đông người.                                                          |
| 4   | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0`             | ☒ Cần sửa | Nhiều ảnh (`train_04`, `train_06`, `train_11`, `train_12`, `train_15`, `train_16`) xóa khớp bị che thành `v = 0`. |
| 5   | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh                   | ☒ Cần sửa | Tổng số `v = 0` là 109 khớp (bài đối chiếu chỉ 72 khớp), 13 ảnh lạm dụng `v = 0` khi người ở giữa hình.           |
| 6   | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý)          | ☑ Đạt     | Không phát hiện điểm trôi nổi do phím tắt `h`.                                                                    |
| 7   | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☑ Đạt     | Định dạng COCO export đúng chuẩn.                                                                                 |
| 8   | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]`                     | ☑ Đạt     | Nhãn YOLO Pose đủ 56 trường số trên mỗi dòng.                                                                     |
| 9   | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau             | ☑ Đạt     | Đã chạy so sánh bảng visibility với nhãn đối chiếu (`%v=1` lệch lớn ở tai, cổ chân, đầu gối, cổ tay, hông).       |
| 10  | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md`                  | ☑ Đạt     | Có tài liệu guideline bổ sung quy tắc ước lượng khớp bị che.                                                      |
| 11  | `check_pose_labels.py` chạy 0 lỗi                                       | ☒ Cần sửa | Còn 15 cảnh báo về lỗi đảo trái/phải (`train_02.txt`) và lạm dụng `v = 0`.                                        |


---

## 2. Danh sách lỗi tìm được trong bài của bạn


| Ảnh            | Người thứ | Khớp                                                 | Lỗi gì                               | Sửa thế nào                                                                                   |
| -------------- | ---------: | ---------------------------------------------------- | ------------------------------------ | --------------------------------------------------------------------------------------------- |
| `train_02.jpg` | 1         | `left_shoulder, right_shoulder, left_hip, right_hip` | Đảo trái/phải (đối xứng qua 2 mắt)   | Đổi lại vị trí và cờ visibility giữa vai trái/phải và hông trái/phải theo chiều cơ thể người. |
| `train_04.jpg` | 1         | `left_ear, right_ear, left_wrist`                    | Thiếu/xoá khớp nhìn thấy hoặc bị che | Ước lượng vị trí tai và cổ tay, chuyển cờ từ `v = 0` sang `v = 1` hoặc `v = 2`.               |
| `train_06.jpg` | 1         | `left_ear, right_ear`                                | Xoá khớp bị che (`v = 0`)            | Ước lượng vị trí tai qua mép tóc và chuyển cờ sang `v = 1`.                                   |
| `train_11.jpg` | 1         | `left_hip, right_hip`                                | Xoá khớp bị che (`v = 0`)            | Áo khoác che hông nhưng người vẫn trong ảnh, ước lượng vị trí hông và gắn `v = 1`.            |
| `train_12.jpg` | 1         | `left_wrist, left_knee, left_ankle`                  | Thiếu khớp và xoá khớp bị che        | Đặt chấm ước lượng cho khuỷu/đầu gối/cổ chân bị che và chuyển cờ sang `v = 1`.                |
| `train_13.jpg` | 2, 3      | Toàn bộ skeleton                                     | Thiếu hẳn 2 người trong ảnh          | Bỏ sót 2 người ở góc trái và phía sau; bổ sung đủ 2 skeleton 17 điểm.                         |
| `train_14.jpg` | 2         | `nose, left_eye, right_eye, right_wrist`             | Thiếu khớp nhìn thấy rõ              | Bổ sung các điểm trên mặt và cổ tay bị khuất nhẹ, chuyển cờ sang `v = 1` hoặc `v = 2`.        |
| `train_15.jpg` | 2         | `left_knee, left_ankle`                              | Xoá khớp bị che (`v = 0`)            | Đầu gối/cổ chân bị che bởi chân kia, cần đặt chấm ước lượng và gắn cờ `v = 1`.                |
| `train_16.jpg` | 2         | `nose, right_eye, right_ear`                         | Xoá/thiếu khớp mặt                   | Xác định lại vị trí mặt bị nghiêng/khuất, ước lượng và gắn `v = 1`.                           |


---

## 3. Phân tích Visibility &amp; Hai câu kết luận

### Bảng so sánh Visibility (%v=1 bạn vs đối chiếu)

So sánh tập nhãn của bạn (`friend/coco_keypoints/train`, 28 skeleton) với tập đối chiếu (`dataset/labels/train`, 29 skeleton):


| # | Khớp | %v=1 (bạn) | %v=1 (đối chiếu) | Lệch |
| ---: | --- | ---: | ---: | ---: |
| 13 | left_knee | 14% | 31% | 17 |
| 15 | left_ankle | 11% | 28% | 17 |
| 10 | right_wrist | 18% | 28% | 10 |
| 16 | right_ankle | 18% | 28% | 10 |
| 11 | left_hip | 21% | 28% | 6 |
| 3 | left_ear | 7% | 24% | 17 |
| 14 | right_knee | 25% | 24% | 1 |
| 4 | right_ear | 7% | 21% | 14 |
| 12 | right_hip | 18% | 21% | 3 |
| 8 | right_elbow | 7% | 14% | 7 |
| 9 | left_wrist | 7% | 14% | 7 |
| 5 | left_shoulder | 11% | 10% | 0 |
| 7 | left_elbow | 7% | 3% | 4 |
| 1 | left_eye | 4% | 3% | 0 |
| 0 | nose | 4% | 0% | 4 |
| 2 | right_eye | 4% | 0% | 4 |
| 6 | right_shoulder | 4% | 0% | 4 |


### Kết luận

- **Lỗi lặp đi lặp lại nhiều nhất của bài này:** Lỗi tự ý xoá các khớp bị che khuất thành cờ `v = 0` (Outside) và bỏ đặt chấm, thay vì ước lượng toạ độ giải phẫu và gắn cờ `v = 1` (Occluded). Thêm vào đó là bỏ sót 2 skeleton ở ảnh đông người (`train_13.jpg`) và mắc 1 lỗi đảo trái/phải ở vai/hông (`train_02.jpg`).
- **Nó là lỗi thao tác hay lỗi guideline chưa rõ?** Lỗi xuất phát từ **guideline ban đầu chưa phân biệt rõ ràng** giữa "bộ phận bị che khuất nhưng vẫn nằm trong khung hình" (`v = 1`) và "bộ phận cơ thể rơi ra ngoài mép ảnh" (`v = 0`). Sau khi so sánh bảng `visibility_compare.md` và thống nhất bổ sung quy tắc này vào `GUIDELINE_MINI.md`, các lỗi tương tự đã có cơ sở rõ ràng để sửa chữa triệt để.

