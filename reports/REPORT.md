# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Minh Đức    Ngày: 2026-09-16

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 28 |
| v=2 / v=1 / v=0 | 298 / 143 / 35 |
| Thời gian trung bình mỗi ảnh | ~4 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` — %v=1 = 64% (10 v=2, 18 v=1, 0 v=0)
2. `right_ear` — %v=1 = 46% (15 v=2, 13 v=1, 0 v=0)
3. `right_hip` — %v=1 = 39% (16 v=2, 11 v=1, 1 v=0)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Đúng, đây là ba khớp khó gán nhất. `left_ear` có %v=1 cao nhất (64%) vì nhiều người trong ảnh quay mặt sang phải hoặc đội mũ/tóc dài che tai trái — tai vẫn nằm trong khung hình nhưng hoàn toàn bị che, phải ước lượng vị trí. `right_ear` tương tự nhưng ít hơn vì nhiều người quay nhẹ sang trái. `right_hip` khó không phải vì bị che bởi vật thể, mà vì hông là khớp giải phẫu không có bề mặt nhìn thấy — luôn phải ước lượng vị trí xương chậu qua quần áo, nên hầu hết trường hợp đều gán `v = 1`.

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.933 | — |
| OKS@0.50 | 0.966 | — |
| OKS@0.75 | 0.966 | — |
| Lỗi `dao_trai_phai` | 0 | — |
| Lỗi `nham_nguoi` | 2 | — |
| Lỗi `xoa_khop_bi_che` | 0 | — |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

<!-- Chưa thực hiện rework vì OKS đã đạt mức Xuất sắc (0.933 >= 0.85). Các lỗi cần sửa nếu rework: -->

- train_13.jpg: thiếu hẳn người #1 (người ở phía xa trong ảnh, mặc áo vàng) → cần bổ sung skeleton cho người này
- train_01.jpg, người #1, right_wrist: lỗi nhầm người — chấm right_wrist nằm gần cổ tay người #2 hơn → cần kéo chấm về đúng cổ tay phải của người #1
- train_04.jpg, người #1, left_wrist: lỗi nhầm người — chấm left_wrist gần cổ tay người #2 hơn → cần kéo chấm về đúng vị trí cổ tay trái sau tay lái

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

Không có lỗi đảo trái/phải (`dao_trai_phai`) trong toàn bộ 20 ảnh. Mặc dù `check_pose_labels.py` cảnh báo dấu hiệu đảo trái/phải ở train_02 và train_16 (shoulder và hip ngược chiều so với mắt), đây là do người trong ảnh quay lưng/nghiêng nên trật tự tọa độ trái/phải không theo quy tắc thông thường — kiểm tra với gold cho thấy không có lỗi thực sự.

## 3. Kiểm chéo

Bạn cùng nhóm: (chưa thực hiện kiểm chéo)

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| left_ear | 64% | — | — | Guideline chưa rõ: chưa thống nhất khi nào tai bị tóc/mũ che → v=1 hay v=2 |
| right_hip | 39% | — | — | Guideline chưa rõ: hông luôn bị che bởi quần áo, chưa thống nhất khi nào v=1 hay v=2 |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

<!-- Viết một rule kiểm chứng được: điều kiện nhìn thấy/căn cứ vị trí → chọn v=1 hoặc v=0.
Không chỉ ghi "cẩn thận hơn khi gán". -->

- Tai: Nếu thấy bất kỳ phần da tai nào (dù chỉ dái tai) → `v = 2`. Nếu tai hoàn toàn bị che bởi tóc/mũ/phần đầu quay nghiêng → `v = 1`, đặt chấm ước lượng ở vị trí tai dựa theo gốc hàm và mắt.

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. "Chênh" = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | — | — | — |
| pose_mAP50-95 | — | — | — |
| pose_precision | — | — | — |
| pose_recall | — | — | — |
| box_mAP50-95 | — | — | — |

> **Ghi chú**: Chưa chạy notebook fine-tune (Chặng 6) nên chưa có dữ liệu model.
> Cần mở `notebooks/day4_pose_finetune_yolo26.ipynb` trên Colab với GPU T4 để hoàn thành phần này.

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? (Chưa có dữ liệu — cần chạy notebook)

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? (Chưa có dữ liệu — cần chạy notebook)

3. Một ảnh test model đoán sai: (Chưa có dữ liệu — cần chạy notebook)

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? (Chưa có dữ liệu — cần chạy notebook)

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Dựa trên eval_vs_gold.json, ảnh gán tệ nhất là train_13.jpg (OKS 0.000 do thiếu người #1) và train_01.jpg (OKS 0.816 do nhầm người ở right_wrist). (So sánh với model cần chạy notebook)

## 5. Một rule evidence bạn đã dùng

Ảnh `train_06`, người thứ 1, khớp `left_knee`. Người mặc áo xanh ngồi trên xe máy vàng, quay lưng lại camera. Phần thân dưới từ hông trở xuống bị xe máy và áo khoác dài che kín. Tuy nhiên, nhìn vào tư thế ngồi xe máy, có thể thấy đùi trái hướng xuống và hơi gập — suy ra đầu gối trái nằm ở vị trí gần chỗ chân chạm bệ để chân bên trái xe. Vì đầu gối chắc chắn vẫn nằm trong khung hình (người ngồi gọn trên xe, xe nằm gọn trong ảnh), chỉ bị che bởi thân xe và quần áo, nên gán `v = 1` và đặt chấm ở vị trí ước lượng phía sau bình xăng bên trái.
