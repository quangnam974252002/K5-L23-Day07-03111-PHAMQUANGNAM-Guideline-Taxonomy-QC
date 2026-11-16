# Guideline gán nhãn — v1 (Beta)

> **Đây là bản do giảng viên viết nhanh. Nó CHƯA đủ dùng.**
> Đừng sửa file này. Hãy copy sang `submission/guidelines/individual/` rồi sửa bản của bạn
> (xem `GUIDE.md`, Pha 1).

- Phiên bản: v0.1
- Ngày:  11/16/2026
- Tác giả: 102
- Loại nhãn: bounding box (hộp chữ nhật) trên ảnh đường phố
- Công cụ: CVAT, xuất COCO 1.0

---

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện **người và phương tiện** trên ảnh đường phố
(ví dụ cho camera giao thông hoặc xe tự lái).

## 2. Lớp và thuộc tính

### Lớp (mỗi vật thuộc đúng một lớp)

| Lớp | Định nghĩa |
|---|---|
| `car` | Ô tô con, có sức chứa < 16 chỗ. |
| `truck` | Xe tải, có cabin và thùng chở hàng tách biệt. |
| `van` | Xe thùng kín cỡ nhỏ, có thùng xe và thân xe liền nhau. |
| `motorcycle` | Xe máy, mô tô. |
| `person` | Người, bao gồm cả ngường lái xe và người đi bộ, người không tham gia giao thông. Không bao gồm vật dụng cá nhân như ô, balo, túi xách |
| `ignore_region` | Vùng không cần gán từng vật. |

### Thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | Mức bị che: 0 là thấy rõ 0-20 %, 1 là bị che ít 20-50% , 2 là bị che nhiều 50 - 80%, 3 là không rõ 80% . |
| `truncated` | `no`, `yes` | Vật bị cắt ở mép ảnh. |
| `can_review` | tick / không | Tick khi bạn không chắc, để người khác xem lại. |

## 3. Quy tắc vẽ box

1. Gán box cho mọi người và phương tiện trong ảnh.
2. Box ôm sát vật.
3. Chọn đúng lớp và thuộc tính.
4. Mỗi object phải có một Box riêng biệt. Các Box được phép chồng lấn khi các đối tượng nằm gần hoặc che khuất lẫn nhau.
5. Người bị che trên 80%, vật quá nhỏ < 20 px có thể bỏ qua, trường hợp có tỷ lệ bị che khuất hoặc bị cắt lớn hơn 80% vẫn khuyến khích gán nhãn nếu còn đủ bằng chứng để xác định vị trí và class.

## 4. Ca khó
- Đối với trường hợp phương tiện có chở phương tiện khác, phải gán nhãn cả phương tiện đang di chở và các phương tiện được chở
- Xe Tuk, rickshaw, xe 3 bánh được tính là motorcycle
- Không gán reflection, bóng đổ hoặc hình ảnh trên billboard/màn hình thành object thật
- Object bị che khuất bởi object khác hoặc bị cắt bởi biên ảnh, với phần bị che khuất hoặc nằm ngoài khung hình không quá 80% kích thước ước tính của đối tượng, cần phải được gán nhãn
- Xe mô tô thùng, xe máy có kèm thêm một thùng chở khách, vẫn phải dán nhãn cả thùng chở
- Không dán nhãn tượng, vật trang trí và các mô hình có hình dạng khớp với object vì các object này không trực tiếp tham gia giao thông, không tính là phương tiện 


## 5. QC và phiên bản

- Mọi thay đổi làm đổi nhãn đều tăng phiên bản guideline.
- Ca mơ hồ: tick `can_review`.
- 