# BID-07 · Biên bản phạt của chủ đầu tư — Kịch bản kiểm thử

> Nguồn: `spec/BID-07.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.
>
> Màn hình này là lớp còn thiếu giữa tờ giấy chủ đầu tư gửi và khoản khấu trừ rơi xuống tổ đội. Trước nó, bốn chỗ trong hệ thống đã nói về khoản phạt mà không chỗ nào giữ bản ghi gốc — nên phần lớn kịch bản nhóm B kiểm **đường đi ra** của con số: sang bảng khấu trừ của PRJ-03, sang báo cáo đánh giá BCH của RPT-02, và hai ô tổng ở ORD-10 / RPT-05.
>
> Hai chiều phạt dễ lẫn nhau: *công ty phạt tổ đội* (Ban chỉ huy tự lập, đã có từ trước ở PRJ-03) và *chủ đầu tư phạt công ty* (màn hình này). UAT-BID07-24 kiểm đúng chỗ hai chiều gặp nhau.
>
> Số liệu ví dụ lấy từ mockup *Đấu thầu › Biên bản phạt chủ đầu tư*: hợp đồng `KH_0114`, biên bản `05/BB-PHAT` ngày 10/09/2026, giá trị 100.000.000, phân bổ `TD-004` 40.000.000 và `TD-007` 35.000.000, phần dư 25.000.000 × hệ số 1,5.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID07-1 | Đăng nhập bằng tài khoản P. Đấu thầu | Mở màn hình biên bản phạt | Có đủ các khối của mockup: khối biên bản, bảng phân bổ về tổ đội, khối phần không phân bổ kèm hệ số, nút *Khoản chờ khấu trừ*, nút *Trình lãnh đạo* | Mockup UI · CN-1…6 |
| UAT-BID07-2 | Hợp đồng `KH_0114` đang hiệu lực | Mở ô **Hợp đồng** | Danh sách chọn, không phải ô gõ tự do; chỉ có hợp đồng P. Đấu thầu đã khởi tạo ở BID-05 | CN-1 · BR-02 · TS · `contract_no` |
| UAT-BID07-3 | Đang lập biên bản mới | Xem ô **Mã hệ thống** | Chỉ đọc, ghi rõ *tự sinh*; sau khi lưu mang dạng `BBP-KH_0114-01` | TS · `penalty_id` |
| UAT-BID07-4 | Lập biên bản thứ hai cho cùng hợp đồng | Lưu | Mã là `BBP-KH_0114-02` — `NN` chạy riêng trong từng hợp đồng, không phải một dãy chung toàn hệ thống | TS · `penalty_id` |
| UAT-BID07-5 | Biên bản 100.000.000, đã phân bổ 75.000.000 | Xem dòng tổng của bảng phân bổ | Hiện *Tổng đã phân bổ 75.000.000* và *còn lại 25.000.000 chưa quy được cho tổ đội nào* | CN-3 · KQ-3 |
| UAT-BID07-6 | Tiếp UAT-BID07-5, nhập hệ số 1,5 | Xem khối phần không phân bổ | *Quy trách nhiệm BCH* hiện 37.500.000 ngay bên dưới, trước khi trình — người đặt hệ số nhìn thấy hệ quả trước khi bấm | CN-4 · KQ-4 |
| UAT-BID07-7 | Hệ số vẫn để 1,0 | Xem ô **Lý do nâng hệ số** | Ô không bắt buộc; nâng lên trên 1,0 thì chuyển thành bắt buộc | CN-4 · BR-07 |
| UAT-BID07-8 | Đăng nhập bằng tài khoản Ban dự án | Mở màn hình biên bản phạt | Đọc được danh sách và chi tiết; **không** có nút tạo biên bản mới | BR-01 · PL-B |
| UAT-BID07-9 | Đăng nhập bằng tài khoản Ban chỉ huy dự án `KH_0114` | Mở một biên bản của hợp đồng đó | Sửa được bảng phân bổ và hệ số; **không** sửa được khối biên bản (số, ngày, giá trị, tệp) | BR-01 · BR-08 · PL-B |
| UAT-BID07-10 | Biên bản đã chuyển *Chờ lãnh đạo chốt* | Mở lại màn hình | Mọi ô khoá; không còn nút *Trình lãnh đạo* | VAL-15 · TS · `status` |
| UAT-BID07-11 | Có 2 dòng phân bổ đã chốt mà tổ đội chưa có kỳ giao khoán | Bấm **Khoản chờ khấu trừ** | Danh sách gom theo tổ đội, mỗi dòng ghi mã biên bản, hợp đồng và giá trị | CN-5 · KQ-7 |
| UAT-BID07-12 | Danh sách biên bản của nhiều kỳ | Đặt bộ lọc khoảng thời gian 01/09 – 30/09/2026 | Lọc theo **ngày biên bản**, không theo ngày nhập; nhãn bộ lọc nói rõ mốc lọc | BR-12 · YC-14 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID07-13 | Hợp đồng `KH_0114`, đã có biên bản số `05/BB-PHAT` | Lập biên bản mới cũng mang số `05/BB-PHAT` | "Hợp đồng {mã} đã có biên bản số {số} ghi ngày {ngày}. Mở biên bản đó hoặc sửa lại số." | VAL-1 · BR-03 |
| UAT-BID07-14 | Cùng số `05/BB-PHAT` nhưng trên hợp đồng `KH_0115` | Lưu | Lưu được — số của chủ đầu tư chỉ duy nhất trong phạm vi một hợp đồng, hai chủ đầu tư đánh trùng số là chuyện bình thường | TS · `issuer_doc_no` |
| UAT-BID07-15 | Biên bản chưa tải tệp nào | Bấm *Trình lãnh đạo* | "Tải tệp biên bản chủ đầu tư gửi trước khi trình — tổ đội cần bản này để đối chiếu khoản bị trừ." | VAL-2 · BR-04 |
| UAT-BID07-16 | Ngày biên bản 10/09/2026 | Nhập ngày nhận 09/09/2026 | "Ngày nhận không thể trước ngày ghi trên biên bản. Kiểm tra lại hai ngày." | VAL-4 |
| UAT-BID07-17 | Biên bản 100.000.000, đã có `TD-004` 40.000.000 | Thêm `TD-007` 70.000.000 | Bị chặn: tổng 110.000.000 vượt giá trị biên bản | VAL-5 · BR-05 |
| UAT-BID07-18 | Biên bản 100.000.000, đã phân bổ 80.000.000 | Sửa giá trị phạt xuống 50.000.000 | "Các dòng phân bổ đang cộng nhiều hơn giá trị mới. Sửa lại phân bổ trước." | VAL-6 |
| UAT-BID07-19 | Đã có một dòng cho `TD-004` | Thêm dòng thứ hai cũng cho `TD-004` | "Tổ đội {tên} đã có một dòng phân bổ trong biên bản này. Gộp vào dòng đó thay vì thêm dòng mới." | VAL-8 |
| UAT-BID07-20 | Phần dư 25.000.000 | Nhập hệ số 0,5 | Bị chặn: hệ số không nhỏ hơn 1,0 — phần không phân bổ ít nhất phải ghi nhận đủ giá trị thật | VAL-11 · BR-07 |
| UAT-BID07-21 | Phần dư 25.000.000, hệ số 1,5, lý do để trống | Bấm *Trình lãnh đạo* | "Ghi căn cứ nâng hệ số lên {số} — con số này đi vào đánh giá hiệu quả của Ban chỉ huy." | VAL-9 · BR-07 |
| UAT-BID07-22 | Phần dư 25.000.000 | Nhập hệ số 2,5 | Cảnh báo "vượt mức thường dùng", **cho đi tiếp** sau khi xác nhận — "hoặc cao hơn" là chữ của yêu cầu gốc | VAL-10 |
| UAT-BID07-23 | Biên bản đủ điều kiện, phân bổ `TD-004` 40.000.000 và `TD-007` 35.000.000, hệ số 1,5 có lý do | Trình, rồi Ban lãnh đạo duyệt | Một phiên duyệt duy nhất mang **cả hai nửa**; duyệt xong biên bản *Đã chốt*, hai dòng phân bổ khoá lại; hai dòng vẫn là 40.000.000 và 35.000.000 — **không** bị nhân hệ số | BR-06 · BR-08 · KQ-5 |
| UAT-BID07-24 | Tiếp UAT-BID07-23, tổ đội `TD-004` lập kỳ giao khoán mới trên hợp đồng `KH_0114` | Mở bảng khấu trừ của kỳ đó ở PRJ-03 | Có dòng phạt 40.000.000; cột **Nguồn** ghi `BBP-KH_0114-01` và mở được tệp PDF gốc; dòng ở chế độ chỉ đọc. Dòng phạt do Ban chỉ huy tự lập trên cùng kỳ vẫn sửa được như cũ | BR-09 · KQ-6 · PRJ-03 |
| UAT-BID07-25 | Tiếp UAT-BID07-24 | Thử sửa giá trị dòng phạt đó ngay tại PRJ-03 | Không sửa được; hướng dẫn quay về BID-07 | BR-09 · PRJ-03 |
| UAT-BID07-26 | Tiếp UAT-BID07-23, `TD-007` chưa có kỳ giao khoán nào | Mở *Khoản chờ khấu trừ* | Dòng 35.000.000 của `TD-007` nằm trong danh sách; hệ thống **không** tự tạo kỳ giao khoán để trừ | BR-10 · KQ-7 |
| UAT-BID07-27 | Tiếp UAT-BID07-23 | Mở ORD-10 và RPT-05 của hợp đồng `KH_0114` | Cả hai ô khoản phạt hiện 100.000.000 ở chế độ chỉ đọc, cùng một con số, bấm vào mở danh sách biên bản | KQ-8 · ORD-10 · RPT-05 |
| UAT-BID07-28 | Tiếp UAT-BID07-23 | Mở RPT-02, khối *Hiệu quả điều hành BCH*, kỳ 6 tháng cuối 2026 | Dòng của Ban chỉ huy dự án có cột phạt CĐT không phân bổ bằng 37.500.000 | KQ-4 · RPT-02 |
| UAT-BID07-29 | Biên bản ngày 10/09/2026, nhập vào hệ thống ngày 05/11/2026 | Mở RPT-02 kỳ quý 3/2026 | Biên bản nằm ở quý 3 theo **ngày biên bản**; kỳ quý 4 không có nó | BR-12 |
| UAT-BID07-30 | Tiếp UAT-BID07-23 | Xem quỹ phạt ở ACC-07 | Phần **đã phân bổ** đi vào quỹ theo đúng luật nguồn của ACC-07 khi kỳ giao khoán khấu trừ; phần **không phân bổ** không vào quỹ — nó là chỉ số đánh giá, không phải tiền ai nộp | ACC-07 · BR-06 |
| UAT-BID07-31 | Hợp đồng `KH_0090` đã thanh lý | Lập biên bản cho hợp đồng đó | Lưu được, kèm cảnh báo "khoản phân bổ sẽ không còn kỳ giao khoán nào để khấu trừ" | VAL-13 |
| UAT-BID07-32 | Tổ đội `TD-021` không nhận khoán trên `KH_0114` | Thêm dòng phân bổ cho `TD-021` | Cảnh báo, **cho đi tiếp** sau khi xác nhận — khoản phạt phải quy đúng người làm, kể cả khi bảng khoán chưa phản ánh | VAL-12 · TS · `alloc[].crew_id` |
| UAT-BID07-33 | Tổ đội `TD-004` đã ngưng hợp tác | Thêm dòng phân bổ cho tổ đó | Chọn được — bản ghi nói ai gây ra vi phạm, không nói ai đang còn làm việc | TS · `alloc[].crew_id` |
| UAT-BID07-34 | Dòng `TD-004` đã khấu trừ ở kỳ `GK-2026-09` | Mở lại biên bản, sửa giá trị dòng đó | Yêu cầu nhập lý do; kỳ giao khoán liên quan được đánh dấu cần tính lại | VAL-14 · BR-09 |
| UAT-BID07-35 | Biên bản đã phân bổ hết 100.000.000, phần dư bằng 0 | Duyệt | Biên bản đóng; không có nhánh nào cho phép "bỏ qua" phần còn lại vì không còn phần nào | BR-11 · PL-A |
| UAT-BID07-36 | Biên bản mới lưu | Kiểm hộp thư của Ban chỉ huy `KH_0114`, P. TQT và kế toán | Cả ba đều có một thông báo, nội dung giống nhau, có mã hợp đồng, số biên bản và giá trị | BR-02 · KQ-2 · PF-04 |
