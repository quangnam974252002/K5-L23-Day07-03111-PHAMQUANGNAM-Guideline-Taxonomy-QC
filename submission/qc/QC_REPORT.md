# Báo cáo QC — K5-L23-Nhom07

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Đặc tả quy chuẩn và QC nhãn bounding box 2D phục vụ huấn luyện và đánh giá mô hình phát hiện phương tiện và người tham gia giao thông; giới hạn trong 40 ảnh đường phố thực tế với nhiều ca biên.
- Nguồn và phép sử dụng dữ liệu: Dữ liệu ảnh tuyển chọn từ nguồn Wikimedia Commons theo các giấy phép Creative Commons (CC BY, CC BY-SA, CC0) ghi nhận đầy đủ tại data/ATTRIBUTION.csv.
- Manifest và checksum nhãn cuối: batch_manifest.json (SHA-256: ee7ee06ec414a9597dfc8def4e7d7d66b0c15b114143c005b144cd8da981e4ea), nhãn cuối labels_calibrated.json (SHA-256: 4c715fad93735b3c237b2283d1e19dbbe2e273fdfd5c8d106163ca51947d80cf).
- Guideline và taxonomy khi gán / khi bàn giao: Khi gán dùng Guideline v1.0 và taxonomy.json v1.0 (bổ sung lớp bus và chuẩn hóa 3 mức occluded_level); khi bàn giao nâng cấp Guideline v1.1 calibrated đính chính câu chữ (editorial) giải quyết 6 ca cờ can_review, giữ nguyên schema taxonomy.

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80/80 lượt (100% độ phủ, 4 thành viên 101, 102, 103, 105 mỗi người hoàn thành đủ 20 ảnh theo phân công xoay vòng).
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 541/541 cặp box ghép, IoU TB: 1.000, đồng thuận lớp: 100.0% (mẫu số 541 cặp box trên 40 ảnh chung).
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 0 box chưa ghép, 0 box loại do ignore, 0 vùng bất đồng ignore.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: img_017.jpg (tượng người cưỡi ngựa, cả hai annotator đều tuân thủ đúng R-STATUE-IGNORE không gán person), img_002.jpg (xe chở xe được phân tách đúng truck cho xe chở và car cho xe được chở theo R-TRUCK-CARGO).

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: Xử lý 6 ca cờ can_review (C001, C002 tại img_005.jpg theo R-EDGE-TRUNCATED; C003, C004 tại img_011.jpg theo R-CUSTOM-MOTO; C005, C006 tại img_012.jpg theo R-PICKUP-CAR), người chốt: 102, kiểm lại: 102 xác nhận hợp lệ, dẫn chi tiết tại calibration_decisions.csv.
- Tác động v1.1 lên nhãn và ảnh: Làm rõ quy tắc câu chữ giải quyết 6 ca nghi ngờ, hạ cờ can_review về false trên 3 ảnh, không làm thay đổi nhãn của 37 ảnh còn lại.
- Tham chiếu bằng chứng khi Rework: Không phát sinh Rework; toàn bộ ca calibration đều được kiểm soát và đồng thuận ở vòng 1.

## 4. Kết quả duyệt

- Vòng QC: 1
- Người review và ngày: 102, 2026-11-17
- Đã duyệt / cả lô: 40/40 (100%)
- Ảnh lỗi / ảnh đã duyệt: 0/40 (0.0%)
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 0/541 (0.0%)
- Vật bỏ sót, ảnh và vùng: 0 vật bỏ sót.
- Ca khó / phần còn lại, lỗi lặp và ví dụ: Đã duyệt 3/3 ảnh ca khó (img_005, img_011, img_012) và 37/37 ảnh còn lại, 0 lỗi lặp, chi tiết tại submission/qc/reviews/round-01/qc_review.csv.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: Máy tự động chặn lỗi tọa độ bbox âm/vượt kích thước ảnh và sai cấu trúc schema; người đọc ảnh trực tiếp phát hiện đối tượng phi thực tế (tượng đá img_017, ma-nơ-canh img_033, hình phản chiếu img_020) để loại trừ.

## 5. Quyết định bàn giao

- Quyết định: Release
- Lý do, người nhận và phạm vi sử dụng: Dữ liệu đạt 100% độ phủ, tỉ lệ lỗi 0.0% (đạt ngưỡng <= 2%), IAA đạt tuyệt đối, đủ điều kiện bàn giao cho đội ngũ kỹ sư thị giác máy tính huấn luyện mô hình Object Detection 2D.
- Nếu còn việc: Không còn tồn đọng, toàn bộ bằng chứng đã kiểm tra đạt chuẩn.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: Ca C001 (img_005.jpg, box [0,74,71,95], rule R-EDGE-TRUNCATED, nhãn car, truncated=yes, occluded_level=0); Ca C003 (img_011.jpg, box [282,364,330,276], rule R-CUSTOM-MOTO, nhãn motorcycle); Ca C005 (img_012.jpg, box [294,246,666,394], rule R-PICKUP-CAR, nhãn car).
