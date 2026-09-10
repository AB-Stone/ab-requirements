# PRJ-02 · Sản lượng thi công — Kịch bản kiểm thử

> Nguồn: `spec/PRJ-02.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.
>
> Một dữ liệu, hai chu kỳ: sản lượng nhập theo tuần để điều hành; đến kỳ nghiệm thu gom lại thành bảng khối lượng đợt và khóa lại.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-PRJ02-1 | Dự án `DA-2026-014` đang mở màn hình Sản lượng thi công, tuần 30/2026 | Chuyển đổi giữa 3 chip "Theo tuần / Theo đợt thanh toán / Lũy kế toàn dự án" | Cùng một bộ dữ liệu hiển thị theo 3 cách nhìn khác nhau, không phải nhập lại số liệu nào | CN-1 · BR-01 |
| UAT-PRJ02-2 | Kỹ sư hiện trường đang nhập sản lượng tuần | Chọn đầu việc | Ô chỉ liệt kê đầu việc có trong BOQ hợp đồng, không gõ tự do | TS · `wbs_id` |
| UAT-PRJ02-3 | Kỹ sư vừa gửi sản lượng tuần, chỉ huy trưởng chưa xác nhận | Xem trạng thái bản ghi | Bản ghi ở trạng thái chờ, hiện trong hàng chờ riêng của chỉ huy trưởng; % hoàn thành và tiến độ dự án CHƯA đổi | CN-3 · BR-02 |
| UAT-PRJ02-4 | Kỳ báo cáo tuần 30 chưa đính bản vẽ thể hiện | Bấm gửi sản lượng | Bị chặn: "Đính kèm bản vẽ thể hiện khối lượng của kỳ này." | VAL-4 · BR-07 |
| UAT-PRJ02-5 | Dòng sản lượng chưa chọn tổ đội | Bấm gửi | Bị chặn: "Chọn tổ đội thực hiện — thiếu thông tin này thì không tính được tiền khoán." | VAL-3 · BR-03 |
| UAT-PRJ02-6 | Nhập nhầm 4.200 thay vì 420 | Bấm gửi | Cảnh báo tại chỗ: "Sản lượng tuần này gấp {x} lần mức trung bình gần đây. Kiểm tra lại đơn vị tính và số liệu." trước khi lưu | VAL-6 · kịch bản gốc UAT-PRJ02-7 |
| UAT-PRJ02-7 | Mốc "Ốp lát mặt đứng tháp A" gồm HM-01 (60% giá trị) và HM-05 (40%), HM-01 đạt 55%, HM-05 đạt 30% | Xem % hoàn thành của mốc | % mốc tính lại tại chỗ = 55%×60% + 30%×40% = 45%; không có ô nhập tay cho % mốc | CN-4 · BR-09 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-PRJ02-8 | Tiếp UAT-PRJ02-3, tổ TD-004 tuần 30 gửi 420 m² cho HM-01 (lũy kế trước 5.148) | Chỉ huy trưởng xác nhận | Lũy kế HM-01 lên 5.568; % hoàn thành hạng mục và tiến độ dự án cập nhật ngay | BR-02 · KQ-2 · kịch bản gốc UAT-PRJ02-1/-2 |
| UAT-PRJ02-9 | Đã nhập sản lượng 4 tuần của đợt 5 | Xem tổng hợp theo đợt thanh toán | Tổng đợt bằng đúng tổng 4 tuần, không phải nhập lại số nào | BR-01 · KQ-1 · kịch bản gốc UAT-PRJ02-3 |
| UAT-PRJ02-10 | Đợt 4 đã chốt (`batch_status = đã chốt`) | Nhập sản lượng có ngày thuộc đợt 4 | Hệ thống tự chuyển bản ghi sang đợt 5 kèm ghi chú kỳ gốc, không cho ghi vào đợt đã khóa | VAL-2 · BR-06 · KQ-4 · kịch bản gốc UAT-PRJ02-4 |
| UAT-PRJ02-11 | BOQ HM-02 là 1.260 m², lũy kế hiện tại 1.240 | Nhập thêm 60 m² (lũy kế thành 1.300, vượt BOQ 40) | Cảnh báo vượt BOQ, yêu cầu chỉ ra phụ lục hợp đồng tương ứng; không chỉ ra thì phần vượt vẫn ghi nhận nhưng tách riêng trong báo cáo | VAL-1 · BR-04 · KQ-3 · kịch bản gốc UAT-PRJ02-5 |
| UAT-PRJ02-12 | Sản lượng tuần 28 nhập sai, cần giảm 40 m² | Nhập dòng điều chỉnh `qty = -40` không kèm lý do | Bị chặn: "Nhập lý do điều chỉnh giảm — bản ghi gốc vẫn được giữ nguyên." Sau khi có lý do, dòng âm lưu được, bản ghi gốc không đổi, giữ vết cả hai | VAL-5 · BR-05 · KQ-4 |
| UAT-PRJ02-13 | Đợt 5 đã đủ sản lượng các tuần | GĐ dự án chốt đợt và xuất bảng nghiệm thu | Số liệu khóa (`batch_status = đã chốt`); xuất Excel theo mẫu chủ đầu tư | CN-5 · KQ-4 · KQ-6 · kịch bản gốc UAT-PRJ02-8 |
| UAT-PRJ02-14 | Ảnh chụp từ ứng dụng di động, ảnh khác tải lên từ máy tính | Kiểm tra siêu dữ liệu của cả hai | Ảnh chụp từ app tự gắn ngày giờ + vị trí, không sửa được; ảnh tải từ máy tính bị đánh dấu "không có siêu dữ liệu hiện trường" | BR-08 · KQ-5 |
| UAT-PRJ02-15 | Dự án đã có tổ TD-004 gắn với các dòng sản lượng đã chốt | Mở PRJ-03 lập hồ sơ giao khoán cho TD-004 | Sản lượng theo tổ đội tổng hợp được để đối chiếu với bảng công nhật | KQ-7 (liên PRJ-03) |
| UAT-PRJ02-16 | Kỹ sư ở công trường mất sóng (MOB-01) | Nhập sản lượng và chụp 2 ảnh | Lưu tạm cục bộ, nhãn "chờ đồng bộ"; tự gửi khi có mạng trở lại | KQ-5 · kịch bản gốc UAT-PRJ02-6 |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-1, UAT-9 · BR-02 → UAT-3, UAT-8 · BR-03 → UAT-5 · BR-04 → UAT-11 · BR-05 → UAT-12 · BR-06 → UAT-10 · BR-07 → UAT-4 · BR-08 → UAT-14 · BR-09 → UAT-7. Đủ 9/9.
- **Kết quả mong đợi**: KQ-1 → UAT-9 · KQ-2 → UAT-8 · KQ-3 → UAT-11 · KQ-4 → UAT-10, UAT-12, UAT-13 · KQ-5 → UAT-14, UAT-16 · KQ-6 → UAT-13 · KQ-7 → UAT-15. Đủ 7/7.
- Tám kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1/-2 → UAT-8 · -3 → UAT-9 · -4 → UAT-10 · -5 → UAT-11 · -6 → UAT-16 · -7 → UAT-6 · -8 → UAT-13.
- Đặc tả **không có** ngưỡng cụ thể cho "sản lượng bất thường" ngoài "gấp > 3 lần trung bình 4 tuần gần đây" — không nói cách tính khi dự án có dưới 4 tuần dữ liệu lịch sử (tuần đầu tiên); ghi khoảng trống ở `PL-C`.
- Đặc tả không nói rõ **ai** (kỹ sư hay chỉ huy trưởng) được sửa dòng điều chỉnh âm sau khi đã lưu, và liệu dòng điều chỉnh có cần xác nhận riêng như dòng gốc hay không.
- Liên màn hình: **BID-05** (BOQ hợp đồng), **PRJ-01** (giá khoán, dùng chung tổ đội/đầu việc), **PRJ-03** (giao khoán nhân công, tiêu thụ sản lượng đã chốt), **ORD-09** (hồ sơ thanh toán khối lượng), **ACC-09** (kết chuyển chi phí), **MOB-01** (nhập từ hiện trường).
