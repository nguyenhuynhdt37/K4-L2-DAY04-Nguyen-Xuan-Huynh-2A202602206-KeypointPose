# Báo cáo Ngày 4 - Keypoint &amp; Pose

Họ tên: Nguyễn Xuân Huỳnh   Nhóm: Lab4-K4   Ngày: 16/09/2026

> Báo cáo tổng kết toàn diện quá trình gán nhãn COCO-17 Keypoint, đối chiếu chất lượng với Gold, quy trình Rework, kết quả huấn luyện mô hình YOLO26n-Pose và phân tích bằng chứng thị giác.

## 1. Nhãn của tôi


| Chỉ số                       | Giá trị                        |
| ---------------------------- | ------------------------------: |
| Số ảnh đã gán                | 20                             |
| Số skeleton                  | 29 (ban đầu 28, sau rework 29) |
| v=2 / v=1 / v=0              | 341 / 80 / 72                  |
| Thời gian trung bình mỗi ảnh | ~4 phút                        |


Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_knee` (31%)
2. `right_wrist` (28%)
3. `left_hip` (28%) / `left_ankle` (28%)

**Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích:**

Các khớp có `%v=1` cao nhất ở trên phản ánh đúng đặc tính giải phẫu học trong các hoạt động đời thường:

- Đầu gối và cổ chân (`left_knee`, `left_ankle`) thường xuyên bị che khuất bởi góc ngồi, bàn ghế, hoặc chân còn lại khi bước đi.
- Cổ tay (`right_wrist`) thường xuyên cử động cầm nắm đồ vật hoặc vung ra phía sau lưng/thân mình.
- Tuy nhiên, khớp **thực sự khó xác định vị trí giải phẫu nhất** lại là khớp hông (`left_hip`, `right_hip`). Khớp hông hầu như không bao giờ nhìn thấy trực tiếp trên bề mặt cơ thể người mặc quần áo, nên luôn đòi hỏi người gán nhãn phải ước lượng giải phẫu dựa trên trục cột sống, ranh giới thắt lưng và chiều dài xương đùi. Do đó, việc hông có tỷ lệ `v=1` cao là minh chứng cho việc tuân thủ đúng quy tắc ước lượng thay vì xoá nhầm thành `v=0`.

---

## 2. Chấm với gold


| Chỉ số                      | Trước rework | Sau rework |
| --------------------------- | ------------: | ----------: |
| OKS trung bình              | 0.916        | 0.953      |
| [OKS@0.50](mailto:OKS@0.50) | 0.966        | 1.000      |
| [OKS@0.75](mailto:OKS@0.75) | 0.897        | 1.000      |
| Lỗi `dao_trai_phai`         | 0            | 0          |
| Lỗi `nham_nguoi`            | 0            | 0          |
| Lỗi `xoa_khop_bi_che`       | 5            | 0          |
| Lỗi `thieu_khop`            | 8            | 0          |
| Lỗi `thieu_nguoi`           | 1            | 0          |


**Tôi đã sửa gì giữa hai lần chạy (ghi cụ thể: tên ảnh + người thứ mấy + keypoint + thao tác sửa):**

- `train_13.jpg` người #1: Bổ sung toàn bộ skeleton 17 điểm cho người đứng xa ở mép trái ảnh (x ≈ 0.093, y ≈ 0.612) bị bỏ sót trong lần gán đầu tiên.
- `train_15.jpg` người #1: Bổ sung toạ độ `right_eye`, `right_ear` (v=2) và ước lượng toạ độ `left_knee` (v=1) thay vì để `v=0`.
- `train_15.jpg` người #2: Bổ sung các khớp bên phải bị che/khuất một phần gồm `right_shoulder`, `right_elbow`, `right_hip` (v=2) thay vì để `v=0`.
- `train_06.jpg` người #1: Bổ sung `left_ear` và `right_ear` (v=2) do ban đầu nhầm tưởng tai bị tóc che hoàn toàn nên để `v=0`.
- `train_11.jpg` người #1: Chuyển `left_hip` và `right_hip` từ `v=0` sang `v=1` (ước lượng vị trí mấu chuyển xương đùi dưới lớp áo khoác dài).
- `train_14.jpg` người #2: Bổ sung toạ độ `right_wrist` (v=2) bị khuất góc tay cầm.
- `train_12.jpg` người #1: Đặt chấm ước lượng cho `left_ankle` và chuyển từ `v=0` sang `v=1` (bị che bởi chân đối diện).
- `train_08.jpg` người #1: Đặt chấm ước lượng cho `left_ankle` và chuyển từ `v=0` sang `v=1`.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

Không có bất kỳ lỗi đảo trái/phải (`dao_trai_phai = 0`) nào trong toàn bộ 20 ảnh ngay từ lần chạy đầu tiên. Quy tắc "Trái/phải tính theo cơ thể người, không tính theo bức ảnh" được áp dụng triệt để trong từng thao tác gán nhãn.

---

## 3. Kiểm chéo

Bạn cùng nhóm: Trần Quang Huy

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:


| Khớp          | Bạn | Họ  | Lệch | Nguyên nhân (guideline hay gán sai?)                                                                     |
| ------------- | ---: | ---: | ----: | -------------------------------------------------------------------------------------------------------- |
| `left_knee`   | 31% | 15% | +16% | Bạn cùng nhóm gán sai: có xu hướng để `v = 0` (Outside) cho khớp bị che thay vì ước lượng và để `v = 1`. |
| `right_wrist` | 28% | 12% | +16% | Guideline chưa nói rõ trường hợp cổ tay cầm vật cản. Bạn cùng nhóm bỏ qua không chấm.                    |
| `left_hip`    | 28% | 14% | +14% | Khác biệt về guideline ước lượng hông khi người mặc quần áo dài/rộng.                                    |


Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- *Bất kỳ khớp nào thuộc về cơ thể nằm trong khung hình nhưng bị che khuất bởi vật thể hoặc bộ phận khác đều phải đặt chấm ước lượng tại vị trí giải phẫu hợp lý và gắn cờ* `v = 1`*. Tuyệt đối chỉ dùng* `v = 0` *khi bộ phận đó đã hoàn toàn nằm ra ngoài biên ảnh hoặc những bộ phận không hướng vào cam và do người đó tự che khuất như đứng quay lưng, đứng ngang, ...*

---

## 4. Model

Số liệu trích xuất từ file `outputs/eval_model.json` sau quá trình huấn luyện và đánh giá trên tập test:


| Chỉ số         | yolo26n-pose gốc | Sau fine-tune | Chênh   |
| -------------- | ----------------: | -------------: | -------: |
| pose_mAP50     | 0.8450           | 0.8450        | 0.0000  |
| pose_mAP50-95  | 0.6853           | 0.6853        | 0.0000  |
| pose_precision | 0.9734           | 0.9745        | +0.0011 |
| pose_recall    | 0.8462           | 0.8462        | 0.0000  |
| box_mAP50-95   | 0.8119           | 0.8054        | -0.0065 |


### Trả lời năm câu hỏi ở cuối notebook

1. `**pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?**  
 `pose_mAP50-95` được giữ nguyên ở mức 0.6853, trong khi `pose_precision` tăng nhẹ (+0.0011 từ 0.9734 lên 0.9745). Do tập dữ liệu train chỉ có 20 ảnh (rất nhỏ so với quy mô COCO), việc model duy trì mAP và tăng nhẹ precision cho thấy nhãn được gán có chất lượng rất cao (OKS 0.953), không gây hiện tượng catastrophic forgetting (quên tri thức cũ) hay làm méo mó các phân phối toạ độ chuẩn của YOLO26-Pose.
2. `**box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm *khớp* dễ hơn? Vì sao?**  
 `box_mAP50-95` đạt 0.8054 - 0.8119, cao hơn đáng kể so với `pose_mAP50-95` (0.6853), chênh lệch khoảng 0.126. Model tìm *người* (bounding box) dễ hơn rất nhiều so với tìm *khớp* (17 keypoints). Lý do: Bounding box chỉ cần nhận diện vùng kết cấu tổng thể của con người trên ảnh (khuôn mặt + thân mình), trong khi pose estimation phải xác định chính xác vị trí toạ độ pixel của từng khớp nhỏ, vốn rất dễ bị xoay hướng, biến dạng theo dáng đứng/ngồi và thường xuyên bị che khuất (occlusion).
3. **Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43 (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):**  
 Trong tập test, ở các ca người bị che khuất nửa dưới bởi vật cản, model mắc lỗi **"trượt hẳn"** tại khớp cổ chân (dự đoán điểm rơi vào vật cản thay vì điểm giải phẫu) và **"lệch nhẹ"** tại khớp khuỷu tay do góc gập tay bị khuất bóng.
4. **Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?**  
 Ảnh có độ bất đồng cao nhất về số người và OKS là `train_13.jpg` (model dự đoán 3 người, ban đầu nhãn của tôi chỉ có 2 người). Sau khi đối chiếu với Gold dataset, **model đã đúng** khi phát hiện được người thứ 3 ở góc trái phía sau (`train_13` người #1). Tôi đã dựa vào ground truth của Gold và trực quan ảnh phóng to để kiểm chứng và thực hiện rework bổ sung skeleton này.
5. **Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?**  
 Có, ảnh `train_13.jpg` chính là ảnh có độ khó cao nhất đối với cả người gán nhãn và model. Bức ảnh này có ánh sáng phức tạp, nhiều người đứng chồng chéo ở các độ sâu khác nhau (người phía trước to rõ, người phía sau nhỏ và tối). Điều này chứng minh rằng các vùng dữ liệu có độ phân giải thấp, đối tượng nhỏ (small objects) và độ tương phản kém là thách thức nội tại chung của cả thị giác con người lẫn thị giác máy tính.

---

## 5. Một rule evidence bạn đã dùng

**Ảnh:** `train_10.jpg`, người thứ 1, khớp: `left_knee`, `right_knee`, `left_ankle`, `right_ankle`.

**Bằng chứng nhìn thấy và căn cứ quyết định:**
Trong ảnh `train_10.jpg`, đối tượng là một người đàn ông đứng tựa sát vào mép dưới của khung hình. Toàn bộ phần thân dưới từ giữa đùi trở xuống bị mép ảnh cắt ngang hoàn toàn. Mặc dù về mặt giải phẫu học, bất kỳ cơ thể người nào cũng có đầu gối và cổ chân, nhưng ranh giới không gian pixel của ảnh kết thúc ở tọa độ y = 1.0. Do đó:

1. Hai khớp đầu gối (`left_knee`, `right_knee`) và hai khớp cổ chân (`left_ankle`, `right_ankle`) đã hoàn toàn nằm ra ngoài khung ảnh.
2. Theo đúng nguyên tắc của bài lab: chỉ gắn cờ `v = 1` cho khớp bị che nhưng *còn trong khung hình*. Đối với khớp đã *ra ngoài mép ảnh*, bắt buộc phải gắn cờ `v = 0` và tuyệt đối không đặt chấm trong canvas.
3. Việc tuân thủ này giúp tránh lỗi ép toạ độ ảo sát biên (boundary clamping), giúp mô hình học đúng phân bố không gian thật của ảnh.

