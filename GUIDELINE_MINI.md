# Mini guideline  |  người gán: Nguyễn Minh Đức  |  ngày: 2026-09-16

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
| Hông của người mặc quần áo dài | Đặt chấm ở giữa khoảng cách giữa rìa thân và mép hông nhìn thấy trên quần/váy, ước lượng vị trí xương chậu, gán `v = 1` (occluded). | Hông là khớp giải phẫu, không nhìn thấy trên bề mặt quần áo. Vị trí ước lượng dựa theo chiều rộng thân người ở eo, chia đôi khoảng cách từ đường giữa thân ra hai bên. Ví dụ: train_01, cả hai người mặc áo/quần dài, hông được ước lượng dựa trên vị trí eo. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Đặt chấm ở vị trí ước lượng sau tóc/mũ, gán `v = 1`. Nếu thấy dù chỉ một phần nhỏ của tai thì vẫn `v = 2`. | Tai bị che bởi tóc hoặc mũ vẫn nằm trong khung hình, chỉ bị occluded chứ không phải outside. Ví dụ: train_04, người đội mũ bảo hiểm kín, tai hoàn toàn bị che → `v = 1`. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp từ hông trở lên: gán bình thường (`v = 2` hoặc `v = 1`). Các khớp nằm ngoài mép ảnh (đầu gối, mắt cá): gán `v = 0`, không đặt chấm. | Khớp ra ngoài khung hình thì không có thông tin để ước lượng → `v = 0`. Ví dụ: train_06, người trên xe máy bị cắt từ hông trở xuống, tất cả khớp chân gán `v = 0`. |
| Cổ tay nằm sau tay lái / sau thân mình | Đặt chấm ở vị trí ước lượng phía sau vật che, gán `v = 1`. | Cổ tay vẫn nằm trong khung hình, chỉ bị che bởi tay lái hoặc thân người → bị occluded. Ví dụ: train_09, cổ tay phải nằm sau thân xe/tay lái → `v = 1`. |
| Hai người chồng lên nhau | Gán riêng biệt từng người. Khớp của người bị che bởi người phía trước gán `v = 1` và đặt chấm ước lượng. Hoàn thành hết một người rồi mới sang người tiếp theo. | Tránh lỗi nhầm người — khi hai skeleton gần nhau rất dễ kéo chấm sang cơ thể bên cạnh. Ví dụ: train_03, hai người đứng gần nhau trên xe, cần gán từng người riêng biệt. |
| Người nhỏ đến mức nào thì không gán nữa | Bộ ảnh này đã được chọn sao cho mọi người đều đủ lớn. Nếu bounding box nhỏ hơn 32×32 pixel thì không gán. Trong bộ 20 ảnh hiện tại, tất cả người đều đủ lớn. | Người quá nhỏ (< 32px) thì không thể phân biệt các khớp, gán sẽ mang tính đoán mò. Ví dụ: train_13, người phía xa (người #1 theo gold) tuy nhỏ nhưng vẫn đủ lớn để gán — đây là ca bị thiếu, cần bổ sung. |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_01`, người thứ `1`, khớp `right_wrist`

- Mơ hồ ở chỗ nào: Hai người đứng gần nhau cùng cầm đĩa pizza, cổ tay phải của người 1 (người phụ nữ bên trái ảnh) nằm rất gần cổ tay của người 2 (người đàn ông bên phải ảnh), khó xác định chấm thuộc cơ thể nào.
- Bạn quyết thế nào: Đặt chấm right_wrist ở vị trí cổ tay phải của người phụ nữ — nhưng chấm bị lệch sang gần cổ tay của người đàn ông hơn.
- Vì sao: Hai người cùng giữ đĩa nên cổ tay rất sát nhau. Khi kéo chấm nhanh dễ đặt nhầm sang cơ thể bên cạnh.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model sẽ học rằng cổ tay phải có thể nằm xa hẳn khỏi khuỷu tay phải → xương cánh tay bị kéo dài bất thường, gây lỗi nhầm người (skeleton chéo).

### Ca 2 - ảnh `train_04`, người thứ `1`, khớp `left_wrist`

- Mơ hồ ở chỗ nào: Người đội mũ bảo hiểm ngồi trên xe, cổ tay trái nằm phía sau tay lái xe, bị che bởi cả xe lẫn cánh tay của người thứ 2 bên cạnh.
- Bạn quyết thế nào: Đặt chấm ở vị trí ước lượng sau tay lái, gán `v = 1`.
- Vì sao: Có thể nhìn thấy khuỷu tay trái hướng xuống, suy ra cổ tay phải nằm ở khoảng giữa tay lái. Tuy nhiên chấm bị đặt gần cổ tay của người bên cạnh hơn → lỗi nhầm người.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model sẽ học rằng cổ tay trái của một người có thể nằm trên cơ thể người khác → khi inference model sẽ gán sai khớp giữa hai người chồng lấn.

### Ca 3 - ảnh `train_09`, người thứ `1`, khớp `left_eye`

- Mơ hồ ở chỗ nào: Người quay lưng lại, chỉ thấy phía sau đầu và một phần mũ. Không thể nhìn thấy mắt nhưng người vẫn nằm gọn trong khung hình.
- Bạn quyết thế nào: Đặt chấm ở vị trí ước lượng của mắt trái (phía sau đầu), gán `v = 1`.
- Vì sao: Mắt bị che hoàn toàn bởi đầu (quay lưng) nhưng vẫn nằm trong khung hình → occluded, không phải outside. Vị trí ước lượng dựa trên vị trí tai và mũi (nếu có).
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu gán `v = 0` (xoá khớp), model sẽ không được dạy rằng mắt tồn tại ở phía sau đầu → khi gặp người quay lưng, model sẽ bỏ qua mắt hoàn toàn thay vì ước lượng vị trí.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_ear` (bạn `64%` / họ `chưa so`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Guideline chưa rõ — chưa thống nhất tiêu chuẩn khi nào tai bị tóc/mũ che một phần thì gán `v = 1` hay `v = 2`. Left_ear có `%v=1` = 64%, cao nhất trong toàn bộ 17 khớp, cho thấy nhiều ảnh có người quay nhẹ hoặc tóc che tai trái.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Nếu tai bị che **hoàn toàn** bởi tóc, mũ, hoặc phần đầu (người quay nghiêng) → `v = 1`. Nếu tai còn thấy **bất kỳ phần nào** (dù chỉ dái tai hoặc vành tai) → `v = 2`. Căn cứ: có nhìn thấy bề mặt da của tai hay không.
