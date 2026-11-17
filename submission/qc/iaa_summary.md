# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 4
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 541 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 541 | 1.000 | 100.0% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 101|102 | 10 | 96 | 1.000 | 100.0% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 101|105 | 10 | 219 | 1.000 | 100.0% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 102|103 | 10 | 91 | 1.000 | 100.0% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 103|105 | 10 | 135 | 1.000 | 100.0% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 6 ({'co_can_review': 6}), trên 3 ảnh cần calibration.
