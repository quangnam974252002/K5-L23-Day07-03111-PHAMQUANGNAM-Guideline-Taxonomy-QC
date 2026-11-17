# Guideline agreed ver 1.0

> Bản guideline cá nhân của thành viên **103** sau khi khảo sát toàn bộ 40 bức ảnh đường phố trong `data/images/`.
> Dùng để đối chiếu và đóng góp trong buổi họp thống nhất quy chuẩn nhóm (Pha 2).

- Phiên bản: v1.0
- Ngày: 2026-11-16

---

## 1. Mục tiêu

- Nhãn bounding box 2D được sử dụng để huấn luyện và đánh giá mô hình thị giác máy tính phát hiện đối tượng (**Object Detection 2D**) phục vụ hệ thống camera giám sát giao thông đô thị và hệ thống hỗ trợ lái xe tự động (ADAS/Autonomous Driving).
- **Hệ quả của sai sót**:
  - *Bỏ sót người (`person`) hoặc người đi xe máy (`motorcycle`)*: Nguy cơ cao dẫn tới tai nạn giao thông nghiêm trọng trong hệ thống xe tự lái.
  - *Gán nhầm các đối tượng giả lập (tượng nghệ thuật, hình in quảng cáo, hình phản chiếu qua gương)*: Làm mô hình phanh khẩn cấp sai lệch (phantom braking) hoặc gây báo động giả trên hệ thống camera đô thị.
  - *Box không ôm sát*: Làm suy giảm chất lượng định vị vị trí (Localization error - IoU thấp).

---

## 2. Lớp và thuộc tính

### Lớp đối tượng (Classes)

| Lớp | Định nghĩa (một câu) | Ví dụ cụ thể | Phản ví dụ / Dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con, xe du lịch, sedan, hatchback, SUV từ 4 đến 9 chỗ phục vụ chở người. | ô tô con trên giàn xe tải `img_002.jpg`. | Xe thùng kín vận chuyển (`van`), xe bán tải lớn có thùng hàng (`truck`), xe đồ chơi (`img_038.jpg`). |
| `truck` | Xe tải chở hàng, xe đầu kéo container, xe bồn hoặc xe chuyên dụng có cabin và thùng hàng/giá chở riêng biệt. | Xe chở ô tô 2 tầng ở `img_002.jpg`, xe tải ở `img_001.jpg`. | Xe van thân liền khối (`van`), xe con (`car`). |
| `van` | Xe tải nhẹ thân liền khối hoặc xe chở đồ cỡ nhỏ có khoang hàng kín liền mạch với cabin. | Xe van `img_005.jpg`, `img_016.jpg`. | Xe ô tô du lịch 7 chỗ (`car`), xe buýt (`bus`). |
| `motorcycle` | Phương tiện cơ giới hai bánh có gắn động cơ (xe máy, xe mô tô, xe gắn máy). | Xe máy di chuyển ở `img_003.jpg`, `img_030.jpg`. | Xe đạp, xích lô không động cơ (`img_006.jpg`, `img_039.jpg` -> không gán hoặc vào `ignore_region`). |
| `person` | Người thật đang hoặc không tham gia giao thông (người đi bộ, người đứng, người ngồi trên xe máy, xe đạp).Không bao gồm vật dụng cá nhân như ô, balo, túi xách | Người đi bộ ở `img_014.jpg`, người lái xe máy ở `img_005.jpg`, `img_008.jpg`. | Tượng đồng (`img_011.jpg`), tượng đá điêu khắc (`img_017.jpg`), ma-nơ-canh trong tủ kính (`img_033.jpg`), hình in quảng cáo (`img_018.jpg`). |
| `bus` *(Đề xuất mới)* | Xe buýt công cộng, xe khách lớn chở nhiều hành khách. | Xe buýt ở `img_009.jpg`, `img_031.jpg`, `img_037.jpg`, `img_040.jpg`. | Xe van (`van`), xe tải (`truck`). |
| `ignore_region` | Vùng đánh dấu khu vực phức tạp không gán từng đối tượng đơn lẻ; việc sót vật trong vùng này không tính là lỗi. | Cụm phương tiện/người dày đặc ở quá xa hậu cảnh, vật cao $< 20\text{ px}$. | Các phương tiện hoặc người đứng đơn lẻ nhìn rõ ở tiền cảnh. |

**Quy định đối với vật ngoài phạm vi taxonomy:**
- Các phương tiện thô sơ không động cơ (xe đạp, xích lô, xe đẩy hàng) ở hậu cảnh xa: Nếu nằm rải rác và kích thước nhỏ thì bỏ qua, nếu tập trung đông đúc thì gom vào vùng `ignore_region`.
- Các đối tượng giả mạo (tượng, tranh ảnh, ma-nơ-canh, hình phản chiếu qua gương): **TUYỆT ĐỐI KHÔNG GÁN BOX**.

---

### Thuộc tính (Attributes)

| Thuộc tính | Kiểu | Các mức | Định nghĩa và ngưỡng đo lường cụ thể |
|---|---|---|---|
| `occluded_level` | Select | `0`, `1`, `2` | Mức độ diện tích nhìn thấy của vật bị che khuất bởi vật thể khác:<br>• Mức bị che: 0 là thấy rõ 0-30 %, 1 là bị che ít 30-80% , 2: là bị che nhiều hơn 80%  |
| `truncated` | Select | `no`, `yes` | Vật có bị cắt ngang bởi mép ảnh hay không:<br>• **`no`**: Vật nằm trọn vẹn trong khung hình.<br>• **`yes`**: Một phần thân của vật bị cắt bởi mép biên ảnh (trên, dưới, trái hoặc phải). |
| `can_review` | Checkbox | `false`, `true` | Đánh dấu ca khó/mơ hồ:<br>• Tick `true` khi người gán lưỡng lự về lớp hoặc ranh giới bbox để đưa ra hội đồng calibration xem xét lại. |

---

## 3. Quy tắc vẽ bounding box

1. **Nguyên tắc bao bọc sát mép nhìn thấy (Visible Bounding Box)**:
   - Box phải tiếp xúc sát các pixel rìa ngoài cùng có thể nhìn thấy của vật thể.
   - **Không** mở rộng box để "đoán mò" phần thân bị che khuất hoặc phần khuất sau vật thể khác.
2. **Quy tắc Người đi xe máy (`person` + `motorcycle`)**:
   - **Vẽ 2 bounding box riêng biệt**:
     - 1 box cho `person`: Ôm sát toàn bộ cơ thể người lái từ đỉnh mũ bảo hiểm/đầu đến hết gót chân.
     - 1 box cho `motorcycle`: Ôm sát toàn bộ chiếc xe máy từ tay lái, thân xe đến hết mép bánh xe.
     - Hai box này sẽ giao nhau (overlap) ở vùng người ngồi lên yên xe.
   - **Vẽ phương tiện ba bánh có động cơ**:
      - Xe Tuk, rickshaw, xe 3 bánh được tính là motorcycle
      - Xe mô tô thùng, xe máy có kèm thêm một thùng chở khách, vẫn phải dán nhãn cả thùng chở
3. **Quy tắc Xe tải chở ô tô (`img_002.jpg`)**:
   - Vẽ 1 box `truck` bao toàn bộ thân xe tải bao gồm cả cabin và toàn bộ hệ thống giàn/giá đỡ hàng 2 tầng.
   - Mỗi chiếc ô tô con chở trên giàn giá đỡ nếu nhìn thấy rõ được vẽ thêm 1 box `car` riêng biệt, gán thuộc tính `occluded_level` phù hợp (thường là 1 hoặc 2 do bị các thanh giàn che).
4. **Quy tắc loại trừ đối tượng phi thực tế (False Positives)**:
   - *Tượng đồng, tượng đá nghệ thuật*: `img_011.jpg`, `img_017.jpg`, `img_027.jpg` -> **Không gán**.
   - *Ma-nơ-canh quần áo trưng bày*: `img_033.jpg` -> **Không gán**.
   - *Mô hình xe đồ chơi trong tủ*: `img_038.jpg` -> **Không gán**.
   - *Chân dung trên biển quảng cáo / áp phích*: `img_018.jpg` -> **Không gán**.
   - *Hình ảnh phản chiếu trong gương chiếu hậu*: `img_020.jpg` -> **Không gán**.
5. **Quy tắc vật quá nhỏ**:
   - Bỏ qua các vật có chiều cao hộp $< 20\text{ px}$, che hơn 80% đến mức ko nhận dạng. Nếu có nhiều vật nhỏ tụ tập thành đám đông ở xa thì khoanh bằng một box `ignore_region`.

---

## 4. Ca khó thực tế trong 40 ảnh

| Ảnh | Tình huống phát sinh | Mã quy tắc đề xuất | Quyết định của tôi | Lý do kỹ thuật |
|---|---|---|---|---|
| `img_002.jpg` | Xe tải chuyên dụng chở nhiều ô tô con trên giàn đỡ 2 tầng. | `R-TRUCK-CARGO` | 1 box `truck` bao trọn xe tải + giàn đỡ; các ô tô trên giàn gán từng box `car` riêng biệt. | Mô hình xe tự lái cần nhận biết cả khối xe tải lớn lẫn các ô tô riêng lẻ để tính toán không gian va chạm. |
| `img_009.jpg` | Xe buýt công cộng cỡ lớn di chuyển trên đường. | `R-BUS` | Gán lớp mới `bus`. | Kích thước và động học khác biệt lớn so với xe con (`car`) và xe thùng (`van`). |
| `img_011.jpg` | Tượng đồng hình người cưỡi ngựa/xe giữa quảng trường. | `R-STATUE-IGNORE` | Không gán box. | Không phải thực thể sống hay phương tiện có khả năng di chuyển gây nguy hiểm giao thông. |
| `img_018.jpg` | Khuôn mặt người in kích thước lớn trên biển hiệu quảng cáo. | `R-BILLBOARD-IGNORE` | Không gán box. | Tránh hiện tượng mô hình nhận diện biển quảng cáo thành người đi bộ cản đường. |
| `img_020.jpg` | Hình ảnh xe và người phản chiếu qua gương chiếu hậu ô tô. | `R-REFLECTION-IGNORE` | Không gán box. | Gương là bề mặt phản chiếu 2D, đối tượng thực sự không nằm ở tọa độ 3D phía trước camera. |
| `img_027.jpg` | Tác phẩm điêu khắc nghệ thuật hình xe bán tải. | `R-STATUE-IGNORE` | Không gán box. | Là vật trang trí cố định, không phải xe cơ giới lưu thông. |
| `img_033.jpg` | Ma-nơ-canh hình người mặc trang phục trong tủ kính cửa hàng. | `R-MANNEQUIN-IGNORE` | Không gán box. | Vật trưng bày tĩnh trong nhà, gán nhầm sẽ làm sai lệch bộ dữ liệu người đi bộ. |
| `img_038.jpg` | Xe ô tô mô hình đồ chơi xếp trong tủ kính. | `R-TOY-IGNORE` | Không gán box. | Kích thước thực tế chỉ vài chục cm và nằm trong tủ trưng bày, không phải phương tiện giao thông. |
---

## 5. QC và quản lý phiên bản

- Mọi điều chỉnh về định nghĩa lớp, thuộc tính hoặc ranh giới gán nhãn sau buổi họp nhóm sẽ được nâng cấp lên phiên bản `v1.0_agreed`.
- Các ca còn bất đồng giữa các thành viên: Tạm thời tick `can_review = true` trên CVAT để phân loại trong buổi họp calibration (Pha 5).

| Phiên bản | Thay đổi chính | Lý do / Dẫn chứng ảnh |
|---|---|---|
| `v0` | Bản khởi đầu của giảng viên | Sơ sài, thiếu định lượng `occluded_level`, chưa có quy tắc xe chở xe, tuk-tuk, tượng... |
| `v0.1-103` | Soạn thảo bản cá nhân của thành viên 103 | Định lượng 4 mức che khuất; bổ sung đề xuất `bus`, `tuk_tuk`; quy định rõ 9 ca khó từ 40 ảnh thực tế. |


---

## 6. Quyết định Calibration (v1.1)

Sau calibration đối chiếu 6 ca cờ can_review từ kết quả IAA (Pha 4), bổ sung 3 điều khoản đính chính 
1. **R-EDGE-TRUNCATED (Ca C001, C002 - img_005.jpg)**: Xe ô tô con bị cắt một phần ở mép ảnh trái nhưng vẫn nhận diện rõ cấu trúc đầu xe thì vẫn gán car với thuộc tính 	runcated=yes, occluded_level=0, bỏ cờ can_review.
2. **R-CUSTOM-MOTO (Ca C003, C004 - img_011.jpg)**: Xe mô tô độ dáng có động cơ cơ giới vẫn được phân loại là motorcycle không tính là tượng trang trí, bỏ cờ can_review.
3. **R-PICKUP-CAR (Ca C005, C006 - img_012.jpg)**: Xe bán tải chở rau quả, đồ dùng cá nhân trên thùng sau vẫn xếp vào nhóm phương tiện chở người, xe con car theo phân loại của nhóm, thùng không tách rời thành container riêng nên không phải truck, bỏ cờ can_review.
