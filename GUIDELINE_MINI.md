# Mini guideline - nhóm: Lab4-K4  |  người gán: Nguyễn Xuân Huỳnh  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Đặt tại mấu chuyển lớn xương đùi (ước lượng dựa theo đường nối từ vai xuống và chiều dài đùi), gắn cờ `v = 1`. | Hông người mặc đồ dài luôn bị che khuất bề mặt giải phẫu, cần ước lượng giải phẫu đồng nhất để skeleton không bị gãy đoạn. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Ước lượng vị trí lỗ tai ngang hàng với khóe mắt và chân cánh mũi, gắn cờ `v = 1`. | Tai vẫn nằm trong giới hạn hình ảnh đầu người, việc ước lượng giúp mô hình học được tỷ lệ khuôn mặt khi bị che. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp nằm ngoài mép ảnh (đầu gối, cổ chân) gắn `v = 0` và không chấm. Khớp hông còn trên mép gán `v = 1` hoặc `v = 2`. | Tránh ép toạ độ giả lập vào ngoài viền ảnh làm sai lệch hàm mất mát bounding box và toạ độ chuẩn hoá. |
| Cổ tay nằm sau tay lái / sau thân mình | Ước lượng vị trí cổ tay dựa theo hướng thẳng của cẳng tay từ khuỷu tay tới, gắn cờ `v = 1`. | Cổ tay vẫn nằm trên mặt phẳng không gian của ảnh, là khớp động quan trọng nối cẳng tay với bàn tay. |
| Hai người chồng lên nhau | Gán dứt điểm từng người từ trước ra sau. Khớp người sau bị người trước che thì đặt chấm ước lượng và gắn cờ `v = 1`. | Tránh hiện tượng nhầm người (xương người này nhảy sang chi người kia) và giữ toàn vẹn 17 điểm cho mỗi cá nhân. |
| Người nhỏ đến mức nào thì không gán nữa | Bounding box có chiều cao < 30 pixel hoặc không nhìn rõ cấu trúc đầu/thân thì bỏ qua. Mọi người nhận diện rõ tư thế đều phải gán đủ 17 điểm. | Đối tượng quá mờ không thể xác định cấu trúc giải phẫu sẽ gây nhiễu cho hàm loss của keypoint detection. |

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_10.jpg`, người thứ `1`, khớp `left_knee, right_knee, left_ankle, right_ankle`

- **Mơ hồ ở chỗ nào:** Người đứng sát mép dưới bức ảnh, phần thân dưới bị cắt ngang đùi. Không rõ nên ước lượng khớp đầu gối sát mép (`v = 1`) hay đánh dấu ra ngoài khung (`v = 0`).
- **Bạn quyết thế nào:** Gán `v = 0` cho cả 4 khớp chân (đầu gối và cổ chân trái/phải), không đặt chấm.
- **Vì sao:** Toạ độ giải phẫu thực tế của khớp gối và cổ chân đã hoàn toàn vượt ra ngoài biên dưới của ảnh.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** Model sẽ học sai quy luật chuẩn hoá toạ độ (bị ép toạ độ sát mép 0.99-1.00) hoặc dự đoán sai toạ độ ngoài biên ảnh.

### Ca 2 - ảnh `train_11.jpg`, người thứ `1`, khớp `left_hip, right_hip`

- **Mơ hồ ở chỗ nào:** Người mặc áo khoác thụng dài phủ qua hông, khó thấy ranh giới xương chậu. Ban đầu có xu hướng để `v = 0` vì không thấy.
- **Bạn quyết thế nào:** Gán `v = 1` (Occluded), ước lượng toạ độ hông theo trục dọc cột sống và điểm bắt đầu của chân.
- **Vì sao:** Khớp hông chắc chắn vẫn nằm trong khung hình và đóng vai trò neo trung tâm kết nối thân trên với thân dưới.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** Xoá khớp hông (`v = 0`) làm skeleton bị đứt gãy, model mất thông tin gốc toạ độ của khung chậu khi học pose 17 điểm.

### Ca 3 - ảnh `train_13.jpg`, người thứ `1`, toàn bộ skeleton

- **Mơ hồ ở chỗ nào:** Người ở góc xa bên trái ảnh có kích thước nhỏ, mờ và bị khuất một phần bởi người đứng trước. Phân vân giữa việc xem đây là background hay người cần gán nhãn.
- **Bạn quyết thế nào:** Gán bổ sung người này đủ 17 điểm, các khớp nhìn thấy để `v = 2`, các khớp bị che để `v = 1`.
- **Vì sao:** Dù ở xa nhưng người này vẫn rõ cấu trúc đầu, thân và chân bước đi (đáp ứng tiêu chí độ bao phủ của Gold).
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** Bỏ sót người thật sẽ khiến model bị phạt false negative hoặc model phát hiện ra người nhưng nhãn ground truth không có, dẫn đến triệt tiêu khả năng phát hiện người nhỏ ở hậu cảnh.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_knee` (bạn `31%` / họ `15%`) và `right_wrist` (bạn `28%` / họ `12%`).
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Guideline ban đầu chưa định nghĩa rõ ranh giới giữa "bị che một phần bởi vật cản" (`v = 1`) và "khuất hẳn nên bỏ qua gán v=0". Bạn cùng nhóm có xu hướng để `v = 0` cho các khớp bị che thay vì ước lượng và để `v = 1`.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: *"Khớp bị che khuất nhưng cơ thể vẫn nằm trong khung ảnh thì bắt buộc phải ước lượng vị trí giải phẫu và đặt cờ v = 1; tuyệt đối không dùng v = 0 nếu bộ phận đó chưa ra ngoài mép ảnh."*
