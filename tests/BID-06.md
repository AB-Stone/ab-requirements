# BID-06 · Áp giá doanh thu cho đơn hàng — Kịch bản kiểm thử

> Nguồn: `spec/BID-06.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.
>
> Màn hình này trước mang mã ORD-06; theo YC-01 nó chuyển sang nhóm Đấu thầu vì áp giá doanh thu là quyết định giá bán của **P. Đấu thầu**, P. TQT chỉ đọc. Số liệu ví dụ lấy từ mockup *Bán hàng › Áp giá đơn hàng* của đặc tả (đơn `DH-2026-155`).
>
> Ràng buộc thiết kế lớn nhất của màn hình nằm ở ghi chú yêu cầu gốc: luồng áp giá **chạy song song** và không được chặn sản xuất, cấp hàng. Nhóm B kiểm điều đó bằng kịch bản chứ không chỉ đọc quy tắc.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID06-1 | Có đơn hàng đã duyệt chưa áp giá | Mở màn hình áp giá doanh thu | Có đủ các khối của mockup: bộ lọc đơn chưa có giá, bảng áp giá theo dòng, ô lý do lệch giá, khối tổng hợp đơn hàng, khối trạng thái luồng tác nghiệp | Mockup UI · CN-1…5 |
| UAT-BID06-2 | 14 đơn đã duyệt chưa áp giá; trong hệ thống còn đơn nháp và đơn bị từ chối | Bật bộ lọc **chưa có giá** | Hiện đúng 14 đơn; đơn nháp, đơn chờ duyệt và đơn bị từ chối không có mặt — đây là hàng chờ việc riêng của P. Đấu thầu, không phải hàng chờ sản xuất | CN-1 · KQ-1 |
| UAT-BID06-3 | Mở bảng áp giá đơn `DH-2026-155` | Xem cột **Đơn giá áp** | Mỗi dòng nạp sẵn đơn giá BOQ hợp đồng của hạng mục tương ứng — dòng "Đá granite đen 600×600×20" là 2.280.000 | CN-2 · BR-03 · TS · `revenue_price` |
| UAT-BID06-4 | Dòng "Viên bo góc R120", đơn giá BOQ 4.100.000 | Nhập 3.750.000 rồi rời ô | Cột **Lệch** tính tại chỗ ra −8,5%; doanh thu dòng cập nhật thành 525.000.000 theo khối lượng 140 | CN-2 · TS · `revenue_amount` |
| UAT-BID06-5 | Tiếp UAT-BID06-4 | Xem ô lý do lệch giá | Ô bật lên và thành bắt buộc, kèm thông điệp "Đơn giá lệch {x}% so với BOQ hợp đồng. Nhập lý do — nội dung này hiện trên màn hình duyệt." | CN-3 · VAL-1 |
| UAT-BID06-6 | Còn dòng lệch quá 5% mà ô lý do vẫn trống | Bấm **Trình duyệt giá** | Bị chặn cho tới khi nhập lý do | CN-3/-4 · BR-04 |
| UAT-BID06-7 | Bảng áp giá đã nhập đủ hai dòng | Xem khối tổng hợp đơn hàng | Hiện doanh thu trước VAT 3.033.000.000, VAT 8% 242.640.000 — giá trị doanh thu hiển thị cả trước và sau VAT | TS · `revenue_amount` |
| UAT-BID06-8 | Đơn đã cấp hàng một phần | Xem ô giá vốn ước tính | Có ghi nguồn của con số ngay bên cạnh, để người duyệt biết đang so với giá xuất kho thực tế hay giá vốn dự toán | BR-06 · TS · `est_cost` |
| UAT-BID06-9 | Đăng nhập bằng tài khoản nhân viên P. TQT | Mở bảng áp giá | Không thấy cột **Biên**, không thấy giá vốn và biên lợi nhuận trong khối tổng hợp | BR-05 · TS · `order_margin` · KQ-5 |
| UAT-BID06-10 | Đơn `DH-2026-155` đang sản xuất, cấp hàng 62% | Xem khối trạng thái luồng tác nghiệp | Hiển thị mốc duyệt đơn, nhà máy, tỷ lệ cấp hàng **chỉ để tham chiếu**; không có nút nào tác động ngược vào luồng tác nghiệp | CN-5 |
| UAT-BID06-11 | Bảng áp giá vừa được Ban lãnh đạo duyệt | Mở danh sách đơn hàng, rồi mở chi tiết đơn | Đơn mang nhãn "đã có giá doanh thu" ở cả hai chỗ | TS · `has_price` · KQ-3 |
| UAT-BID06-12 | Đăng nhập bằng tài khoản P. TQT, đơn đã có bảng áp giá | Bấm nhãn "đã có giá doanh thu" trên danh sách đơn | Bảng áp giá mở ở chế độ chỉ đọc: mọi ô khoá, không có nút **Lưu**, không có nút **Trình duyệt giá** | BR-08 · CN-4 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID06-13 | Một đơn ở Nháp, một đơn Chờ duyệt, một đơn bị từ chối, một đơn đã huỷ | Mở hàng chờ áp giá | Không đơn nào trong bốn đơn xuất hiện — chỉ đơn đã duyệt mới áp giá được | BR-01 · TS · `order_id` |
| UAT-BID06-14 | Đơn đã huỷ sau khi duyệt | Mở bảng áp giá của đơn đó bằng liên kết trực tiếp | "Đơn {mã} đã huỷ ngày {ngày}, không cần áp giá." | VAL-4 · BR-01 |
| UAT-BID06-15 | Đơn có một dòng là đầu việc phát sinh, không có trong BOQ hợp đồng | Mở bảng áp giá, xem dòng đó | Đơn giá để trống chứ không gợi ý bừa; "Hạng mục {mã} không có trong BOQ hợp đồng. Chọn căn cứ áp giá: theo phụ lục, giá nội bộ, hoặc khác." — phải chọn `price_basis` khác rồi mới nhập giá | BR-03 · VAL-2 · TS · `price_basis` |
| UAT-BID06-16 | Một dòng là hàng cấp không thu tiền | Nhập đơn giá 0 và lưu | "Đơn giá bằng 0 sẽ ghi nhận doanh thu bằng 0 cho dòng này. Xác nhận nếu là hàng cấp không thu tiền." — xác nhận thì lưu được, đây là cảnh báo không phải chặn | VAL-3 |
| UAT-BID06-17 | Dòng lệch −8,5% đã có lý do "Viên bo góc thực tế cắt từ tấm dư, thống nhất với CĐT giảm giá theo biên bản 12/07" | Trình duyệt giá, rồi mở màn hình duyệt của Ban lãnh đạo | Lý do lệch giá hiển thị nổi bật trên màn hình duyệt, nguyên văn người áp giá đã nhập | BR-04 · KQ-4 |
| UAT-BID06-18 | Đơn `DH-2026-155` đang cấp hàng đợt 2/3 | Áp giá, trình duyệt, Ban lãnh đạo duyệt giá | Trạng thái sản xuất và cấp hàng không đổi; đợt 3 vẫn lập được bình thường | BR-02 · KQ-2 |
| UAT-BID06-19 | Đơn đã duyệt nhưng chưa áp giá (`has_price` = tắt) | Chạy tiếp luồng tác nghiệp: lập đợt cấp hàng, cấp hàng, lập hồ sơ thanh toán | Không bước nào bị chặn — nhãn `has_price` là dữ liệu một chiều, luồng tác nghiệp hiển thị nó nhưng không dùng làm điều kiện | BR-02 |
| UAT-BID06-20 | Bảng áp giá đã trình | Xem chuỗi duyệt | Đúng **một cấp** — Ban lãnh đạo; duyệt xong `has_price` bật | CN-4 · TS · `has_price` |
| UAT-BID06-21 | Đơn vừa được duyệt giá | Mở báo cáo lãi lỗ của dự án | Doanh thu của đơn đã vào sổ; tổng doanh thu các đơn đã duyệt giá khớp với số liệu doanh thu dự án | BR-07 · KQ-6 |
| UAT-BID06-22 | Dự án có đơn hàng nhưng chưa đơn nào áp giá | Mở báo cáo lãi lỗ dự án (RPT-03) | Doanh thu bằng 0 kèm cảnh báo "chưa áp giá đơn hàng"; không trình bày phần lỗ đó như số thật | BR-07 |
| UAT-BID06-23 | Một đơn đã cấp hàng, một đơn chưa cấp | Xem giá vốn ước tính của từng đơn | Đơn đã cấp lấy theo giá xuất kho thực tế; đơn chưa cấp lấy giá vốn dự toán từ BID-03 | BR-06 · TS · `est_cost` |
| UAT-BID06-24 | Vừa bấm **Trình duyệt giá** | Kiểm tra thông báo Ban lãnh đạo nhận được | Thông báo nêu rõ đơn hàng nào, dự án nào, hạng mục nào — cùng chuẩn với thông báo trình đơn ở ORD-03 | KQ-7 |
| UAT-BID06-25 | Đăng nhập bằng tài khoản P. Đấu thầu | Sửa đơn giá một dòng và lưu | Lưu được — bảng áp giá do P. Đấu thầu lập và sửa; chủ sở hữu thao tác khai ở PL-B | BR-08 |
| UAT-BID06-26 | Đăng nhập bằng tài khoản P. TQT (người lập đơn hàng) | Mở danh sách đơn, tìm đơn của mình | Thấy được đơn nào đã có giá để biết hồ sơ đã khép hay chưa, và mở sang bảng áp giá ở chế độ chỉ đọc — đọc được nhưng không ghi được | BR-08 · KQ-3 |
| UAT-BID06-27 | Người dùng không thuộc Ban lãnh đạo, P. Kế toán, P. TQT hay P. Đấu thầu | Mở màn hình áp giá | Không truy cập được | Quyền truy cập (PL-B) |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-13/-14 · BR-02 → UAT-18/-19 · BR-03 → UAT-3/-15 · BR-04 → UAT-6/-17 · BR-05 → UAT-9 · BR-06 → UAT-8/-23 · BR-07 → UAT-21/-22 · BR-08 → UAT-12/-25/-26. Đủ 8/8.
- **Kiểm tra dữ liệu & thông điệp**: VAL-1 → UAT-5 · VAL-2 → UAT-15 · VAL-3 → UAT-16 · VAL-4 → UAT-14. Đủ 4/4.
- **Kết quả mong đợi**: KQ-1 → UAT-2 · KQ-2 → UAT-18 · KQ-3 → UAT-11/-26 · KQ-4 → UAT-17 · KQ-5 → UAT-9 · KQ-6 → UAT-21 · KQ-7 → UAT-24. Đủ 7/7.
- **Chức năng trên màn hình**: CN-1 → UAT-2 · CN-2 → UAT-3/-4 · CN-3 → UAT-5/-6 · CN-4 → UAT-12/-20 · CN-5 → UAT-10. Đủ 5/5.
- Bảy kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1 → UAT-2/-13 · -2 → UAT-18 · -3 → UAT-4/-5 · -4 → UAT-9 · -5 → UAT-11/-21 · -6 → UAT-22 · -7 → UAT-12.
- **Khoảng trống của đặc tả** — ghi lại chứ không tự đặt quy tắc: (a) đặc tả không nói bảng áp giá **đã duyệt** có sửa lại được không, sửa thì có phiên bản và có duyệt lại không, nên không có kịch bản cho đường đó; (b) không nói `has_price` xử lý thế nào khi đơn bị huỷ **sau** khi đã duyệt giá; (c) thuế suất VAT chỉ xuất hiện ở mockup (8%), đặc tả không khai nguồn của nó trong *Tham số*; (d) mockup ghi chú "chỉ Ban lãnh đạo và P. Kế toán thấy **khối này**" cho cả khối tổng hợp, còn BR-05 chỉ giới hạn **cột giá vốn và biên lợi nhuận** — UAT-9 kiểm theo BR-05, phần doanh thu và VAT coi như ai cũng thấy cho tới khi đặc tả nói khác.
- Ngưỡng giải trình 5% là ngưỡng của riêng màn hình này, khác ngưỡng lệch 10% của BID-02 — không trộn hai con số khi dựng dữ liệu kiểm thử.
- Liên màn hình: **ORD-03** (đơn hàng, chuẩn thông báo), **ORD-04/-05** (trạng thái sản xuất, cấp hàng — luồng không bị chặn), **BID-03** (giá vốn dự toán), **BID-05** (đơn giá BOQ hợp đồng), **ACC-09** (giá thành & lãi lỗ), **RPT-03** (cảnh báo chưa áp giá), **PL-B** (quyền ghi/đọc).
