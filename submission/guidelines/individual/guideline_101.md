# Guideline gán nhãn — <0.0.1> — <101>

> Copy file này (hoặc `guideline_v0_starter.md`) rồi viết. Một guideline đủ gồm **5 phần**. Câu nào của v0
> làm hai người đọc ra hai cách khác nhau thì phải viết lại cho một cách hiểu duy nhất.

- Phiên bản: 0.0.1
- Ngày: 16/11/2026
- Người viết / duyệt: Phạm Quang Nam

## 1. Mục tiêu
Mô hình nào dùng nhãn này, để làm gì, sai ở đâu thì nguy hiểm.

Mô hình 

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| car | xe ô tô con 4 bánh | xe taxi 4 chỗ, 7 chỗ | xe van |
| truck | xe tải | xe tải trọng lượng lớn  | xe van |
| van | xe ô tô có đuôi xe chữ nhật | xe taxi 4 chỗ, 7 chỗ | xe van |
| motocycle | xe 2 bánh | xe máy | xe đạp |
| person | sinh vật loài người có thể đi lại mà không phải là vật, ảnh hoặc trong 1 thiết bị mà có khả năng chơi video  | người lớn | tượng |
| ignore-region | vùng bỏ qua |  | nhầm với class phải dán nhãn|

Vật **không thuộc lớp nào** thì làm gì (không gán? vùng ignore? lớp mới?).
Cho gán chung với lớp ignore-region

### Thuộc tính
Mỗi thuộc tính: tên, **các mức cố định**, nghĩa từng mức, **ngưỡng cụ thể** (ví dụ che bao nhiêu % thì mức nào).
occluded_level :  vật bị che 10% thì để occluded_level 10% | 
truncated : nếu hơn 5% vật bị cắt ngoài ảnh thì để truncated

## 3. Quy tắc vẽ box
Box ôm sát phần nhìn thấy; vật bị che; vật bị cắt mép ảnh; phản chiếu / hình in / tượng / đồ chơi; xe chủ (ego); vật quá nhỏ; đám đông; một vật có nhiều phần (người và xe)…

Box phải ôm sát vật có thể nhìn thấy.
Vật bị che hơn 80-90% hoặc bị che đến mức không thể nhận dạng thì có thể bỏ qua, không thì vẫn gán và thêm attribute occluded và độ bị che của nó, với level càng cao = độ level mà vật bị che
Vật bị cắt mép ảnh không quá 90% thì vẫn gán nhưng để thêm attribute truncated
Vật mà trong ảnh, phản chiếu, trên tivi, hình in đều không gán
Tượng và đô chơi thuộc 1 trong class trên cũng không gán
Đối với vật thuộc class nào mà dưới 20-30px về w:h ( vd : w30 h30) thì bỏ không gán.
Đám dông và có nhiều phần vẫn gán như bình thường, có thể bị gán đề

## 4. Ca khó
| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| img_014 | xe máy trong ảnh camera không bắt được, dẫn dến motion blur | 01 | gán cả đoạn mờ nếu vẫn trong tầm nhận diện được | trong thực tế như bắt lỗi hoặc tôi phạm giao thông, nếu mà AI phải xử lý trường hợp tương tự như trên mà không bắt được thì sẽ dẫn đến hậu quả khôn lường |
| img_014 | xe ô tô bị mất một phần trong ảnh do mờ/blur | 02 | vẫn gán bình thường | lý do như img_014 |
| img_017 | tượng hình người cưỡi ngựa đá | 03 | không bbox | AI khi nhận diện nhầm tượng là người có thể dẫn đến trường hợp khó lường khi đưa vào trong 1 địa điểm đầy tượng như bảo tàng, có thể dẫn đến hậu quả không đoán trước |
| img_018 | người trong advertisement broad | 04 | không bbox | lý do như img_017 nhưng thay tượng với ảnh người |
| img_019 | người lái xe ô tô tự chế | 05 | can_review |  |
| img_020 | xe ô tô được phản chiếu lại trong gương | 06 | không bbox | can_review |
| img_024 | mô hình ô tô giả trên ô tô thật | 07 | không bbox | can_review |
| img_027 | mô hình ô tô giả trên mộ | 08 | không bbox | can_review |
| img_032 | ô tô bị hàng người đứng che đến mức khó nhận dạng | 08 | bbox nhưng ô tô nào mà có thể nhận dạng được 10% bộ phận ô tô | can_review |
| img_033 | mô hình người ma nơ canh | 09 | không bbox | lý do như img_033 nhưng thay tượng với ma nơ canh |
| img_038 | đồ chơi trẻ con | 10 | không bbox | can_review |

Nếu chưa chốt, đánh dấu `can_review` trên box hoặc ghi vấn đề cấp ảnh. Dùng mã quy tắc trong nhật ký QC.

## 5. QC và phiên bản
Ngưỡng QC áp dụng, ai quyết ca mơ hồ, lịch sử thay đổi:

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| | | |
