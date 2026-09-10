# BID-05 · Ký hợp đồng & khởi tạo dự án — Kịch bản kiểm thử

> Nguồn: `spec/BID-05.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS-HĐ` / `TS-DA` = bảng *Tham số — hợp đồng* / *— khởi tạo dự án* · `CHK` = bảng *Tham số — danh mục kiểm tra trước ký* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID05-1 | Gói thầu đã chốt giá ở BID-03 | Mở màn hình hợp đồng | Có đủ các khối của mockup: kiểm tra trước ký, tiến trình ký số, thông tin hợp đồng, đợt thanh toán, thành phần dự án, nhân sự hiện trường | Mockup UI · CN-1…8 |
| UAT-BID05-2 | Hợp đồng soạn xong, `precheck` = chưa chạy | Xem khối kiểm tra trước ký | Liệt kê đủ 10 hạng mục kiểm tra kèm đối tượng đối chiếu và mức (Chặn / Cảnh báo) | CHK · TS-HĐ · `precheck` |
| UAT-BID05-3 | Vừa chạy kiểm tra xong | Xem kết quả | Mỗi hạng mục hiện kết quả **kèm số liệu đối chiếu**, không chỉ Đạt / Không đạt suông | CN-1 |
| UAT-BID05-4 | Kết quả kiểm tra còn mục ở mức Cảnh báo chưa xác nhận | Xem nút **Trình ký** | Nút còn khoá; mục cảnh báo phải được người kiểm tra xác nhận kèm lý do ngay tại dòng đó | CN-1 · KQ-1 · VAL-3 |
| UAT-BID05-5 | Chuỗi ký đang chạy | Xem khối tiến trình ký số | Từng bước hiện người ký, thời điểm, phương thức; bước đang chờ được làm nổi, biết ngay đang chờ ai ký | CN-2 · KQ-3 |
| UAT-BID05-6 | Đang khai thông tin hợp đồng | Nhập giá trị trước VAT và thuế suất | Giá trị sau VAT tính tự động | CN-4 · TS-HĐ · `contract_value` |
| UAT-BID05-7 | Bảng đợt thanh toán đang có nhiều đợt | Xem bảng | Mỗi đợt có tên, điều kiện, tỷ lệ %, giá trị, ngày dự kiến; bảng có dòng tổng tỷ lệ | CN-5 · TS-HĐ · `payment_terms[]` |
| UAT-BID05-8 | Chưa gán Giám đốc dự án | Mở khối nhân sự hiện trường | Khối bị khoá — phần phân công hiện trường chỉ mở sau khi đã gán Giám đốc dự án | BR-09 · CN-7 |
| UAT-BID05-9 | Đối tác chưa ký | Bấm **Gửi nhắc ký** | Gửi nhắc tới người ký nội bộ hoặc email tới đối tác; số lần nhắc được ghi lại | CN-3 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID05-10 | `precheck` = chưa chạy | Bấm **Trình ký** | "Chạy kiểm tra hợp đồng trước khi trình ký. Chuỗi ký chỉ mở khi kết quả đạt." | VAL-1 · BR-01 |
| UAT-BID05-11 | Giá trị hợp đồng lệch quá 1% so với giá trị báo giá đã chốt ở BID-03 | Chạy kiểm tra hợp đồng | `chk_value` báo Không đạt (mức Chặn): "Giá trị hợp đồng lệch {x}% so với BOQ báo giá đã chốt. Kiểm tra lại trước khi trình ký."; nút Trình ký vẫn khoá | CHK · `chk_value` · VAL-10 |
| UAT-BID05-12 | BOQ hợp đồng còn dòng lệch khối lượng hoặc đơn giá so với BOQ báo giá đã chốt | Chạy kiểm tra | `chk_boq` báo Không đạt (mức Chặn); thông điệp tổng "Còn {n} mục chưa đạt: {danh sách}. Xử lý xong mới trình ký được." | CHK · `chk_boq` · VAL-2 |
| UAT-BID05-13 | Tổng tỷ lệ các đợt thanh toán khác 100% | Bấm **Lưu hợp đồng**, rồi chạy kiểm tra | Lưu bị chặn: "Tổng tỷ lệ các đợt đang là {x}%. Điều chỉnh để đủ 100% giá trị hợp đồng."; `chk_payment` cũng báo Không đạt | VAL-5 · BR-08 · CHK · `chk_payment` |
| UAT-BID05-14 | Hồ sơ đối tác thiếu trường bắt buộc (mã số thuế, địa chỉ hoặc người đại diện) | Chạy kiểm tra | `chk_partner` báo Không đạt (mức Chặn), đối chiếu với hồ sơ đối tác ở PF-01 | CHK · `chk_partner` |
| UAT-BID05-15 | Chưa đính kèm tệp hợp đồng, hoặc tệp đính kèm không phải phiên bản mới nhất trên PF-05 | Chạy kiểm tra | `chk_doc` báo Không đạt (mức Chặn) | CHK · `chk_doc` |
| UAT-BID05-16 | Điều khoản thanh toán khác điều kiện thương mại đã chốt ở BID-03 | Chạy kiểm tra, ghi lý do và xác nhận mục cảnh báo | `chk_terms` ở mức Cảnh báo: "Điều khoản thanh toán khác báo giá đã chốt ({nội dung cũ}). Xác nhận nếu đã thống nhất lại với đối tác."; xác nhận xong kết quả chuyển Đạt và chuỗi ký mở | CHK · `chk_terms` · VAL-8 · KQ-1 |
| UAT-BID05-17 | Hợp đồng yêu cầu bảo lãnh mà `guarantees[]` chưa khai | Chạy kiểm tra | `chk_guarantee` ở mức Cảnh báo, phải xác nhận mới trình ký được | CHK · `chk_guarantee` |
| UAT-BID05-18 | `effective_from` sớm hơn `signed_at`, hoặc hiệu lực để trống | Chạy kiểm tra | `chk_effective` cảnh báo: "Ngày hiệu lực sớm hơn ngày ký. Xác nhận nếu hợp đồng có hiệu lực hồi tố." | CHK · `chk_effective` · VAL-9 |
| UAT-BID05-19 | Chuyên viên Đấu thầu phụ trách gói chạy kiểm tra | Hoàn tất và ký xác nhận kết quả kiểm tra, rồi mở chuỗi ký | `chk_owner` yêu cầu chính chuyên viên phụ trách ký xác nhận; **TP Đấu thầu** mới là người ký số bước 1 — hai vai tách nhau | CHK · `chk_owner` · BR-03 |
| UAT-BID05-20 | Kiểm tra đã đạt, chuỗi ký vừa mở | Trình ký nội bộ | Ba bước ký nội bộ chạy **tuần tự**: chỉ TP Đấu thầu ký được trước, Kế toán trưởng và Tổng GĐ chưa vào hàng chờ | BR-04 |
| UAT-BID05-21 | Kiểm tra đã đạt, TP Đấu thầu đã ký bước 1 | Sửa giá trị hợp đồng rồi lưu | "Hợp đồng vừa thay đổi nên kết quả kiểm tra hết hiệu lực, các chữ ký đã có sẽ bị huỷ. Chạy kiểm tra lại?" — `precheck` về chưa chạy, chữ ký bước 1 bị huỷ hiệu lực, chuỗi ký chạy lại từ bước 1 | VAL-4 · BR-02 · KQ-2 |
| UAT-BID05-22 | Kiểm tra đã đạt, chưa ký bước nào | Lần lượt đổi một điều khoản, một đợt thanh toán, một bảo lãnh, rồi thay tệp bản thảo | Mỗi thay đổi đều đưa `precheck` về chưa chạy — không riêng gì thay đổi giá trị | BR-02 · KQ-2 |
| UAT-BID05-23 | Đủ 3 chữ ký nội bộ, chưa có chữ ký đối tác | Bấm **Khởi tạo & thông báo** | "Còn chờ {người/đối tác} ký. Chỉ khởi tạo dự án khi hợp đồng đã ký đủ." | VAL-6 · BR-05 · KQ-4 |
| UAT-BID05-24 | Đối tác ký ngoài hệ thống | Người phụ trách tải bản ký lên và xác nhận | Hệ thống theo dõi trạng thái bước đối tác chứ không điều khiển bước này; ngày ký của hợp đồng lấy theo thời điểm chữ ký cuối cùng | BR-04 · TS-HĐ · `signed_at` |
| UAT-BID05-25 | Đã ký đủ hai bên, đã gán đủ nhân sự | Bấm **Khởi tạo & thông báo** | Sinh đồng thời bảy kết quả: mã dự án, BOQ hợp đồng, danh mục hạng mục, kho dự án, bảng đợt thanh toán, danh sách thành phần và các thông báo | BR-06 · CN-8 · KQ-5 |
| UAT-BID05-26 | Gói thầu mang mã `KH_0112` | Xem mã dự án vừa sinh | Dự án lấy nguyên mã gói thầu của BID-01 — hợp đồng và dự án cùng một mã | TS-DA · `project_id` |
| UAT-BID05-27 | Mô phỏng lỗi ở một bước của quá trình khởi tạo | Bấm **Khởi tạo & thông báo** | Chạy trong một giao dịch — lỗi ở bất kỳ bước nào thì hoàn tác toàn bộ, không để dự án sinh ra thiếu bộ phận | BR-06 |
| UAT-BID05-28 | Dự án vừa khởi tạo | Mở BOQ hợp đồng, thử sửa khối lượng và đơn giá | Cả hai khoá vĩnh viễn; thay đổi chỉ qua phụ lục hợp đồng, sinh phiên bản BOQ mới và giữ nguyên bản gốc để đối chiếu | BR-07 · KQ-6 · TS-DA · `contract_boq[]` |
| UAT-BID05-29 | Tiếp UAT-BID05-25 | Kiểm tra thông báo của từng người được gán | Mỗi người nhận đúng một thông báo nêu tên dự án và **vai trò của chính họ**; không có thông báo chung gửi cả nhóm | BR-10 · KQ-7 |
| UAT-BID05-30 | Vừa được gán vào dự án với một vai trò | Người đó đăng nhập | Tự động có quyền xem dữ liệu của dự án theo PF-02, không cần Admin cấp riêng | KQ-8 |
| UAT-BID05-31 | Đã gán Giám đốc dự án | Giám đốc dự án phân công chỉ huy trưởng, cán bộ kỹ thuật, thủ kho công trình theo hạng mục | Gán nhân sự hai tầng: P. Đấu thầu gán thành phần dự án, Giám đốc dự án — sau khi được gán — mới phân công nhân sự hiện trường; chưa gán GĐ dự án thì báo "Gán Giám đốc dự án trước — người này sẽ phân công nhân sự hiện trường." | BR-09 · VAL-7 · CN-6/-7 · TS-DA · `members[]`, `site_team[]` |
| UAT-BID05-32 | Dự án vừa khởi tạo | Mở danh sách công trình | Dự án xuất hiện ngay với tiến độ 0% và đủ 4 tab; tab Thanh toán sinh trực tiếp từ bảng đợt thanh toán | KQ-9 · CN-5 |
| UAT-BID05-33 | Tiếp UAT-BID05-25 | Mở danh mục kho (ACC-02) | Có một kho dự án sinh tự động khi khởi tạo, mã suy từ mã dự án | TS-DA · `project_wh` |
| UAT-BID05-34 | Bảo lãnh còn 15 ngày là hết hạn | Chờ tới mốc nhắc | Kế toán trưởng nhận nhắc việc gia hạn bảo lãnh | BR-11 · KQ-10 · TS-HĐ · `guarantees[]` |
| UAT-BID05-35 | Bảo lãnh đã hết hạn mà chưa gia hạn | Mở bảng kê hợp đồng ở RPT-02 | Dòng hợp đồng gắn cờ đỏ | BR-11 |
| UAT-BID05-36 | Hợp đồng gần nhất ở Khánh Hoà là `KH_0113`; có hợp đồng cũ mang mã `HD-YYYY-NNN` | Lưu hợp đồng mới ở Khánh Hoà | Số hợp đồng là `KH_0114`; hợp đồng cũ theo quy ước cũ giữ nguyên, không đánh số lại | TS-HĐ · `contract_no` |
| UAT-BID05-37 | Một công trình ở Quảng Ninh; một công trình không rút được địa điểm | Lưu hai hợp đồng | Tiền tố lần lượt là `MC` (lịch sử theo sổ) và `NL` | TS-HĐ · `contract_no` |
| UAT-BID05-38 | Sổ hợp đồng của một tiền tố có khoảng trống và có mã dùng lại cho phát sinh | Lưu hợp đồng mới | Số cấp là số lớn nhất đang dùng cộng một, không đếm theo số dòng | TS-HĐ · `contract_no` |
| UAT-BID05-39 | Dự án vừa khởi tạo | Mở danh mục hạng mục của dự án | WBS sinh từ mã hạng mục của BOQ hợp đồng; vùng miền suy ra từ địa điểm công trình | TS-DA · `wbs[]`, `region_stat` |
| UAT-BID05-40 | Hợp đồng soạn xong; BOQ hợp đồng 5 dòng, riêng dòng 4 để trống khối lượng — trạng thái hợp lệ mang sang từ BID-01 kèm ghi chú "chờ làm rõ" | Chạy kiểm tra hợp đồng | `chk_qty` báo **Không đạt** ở mức Chặn và nêu dòng 4; nút **Trình ký** khoá vì danh mục còn mục mức Chặn | CHK · `chk_qty` |
| UAT-BID05-41 | Tiếp UAT-BID05-40 | Mở chi tiết mục `chk_qty` trong khối kiểm tra trước ký | "BOQ hợp đồng còn 1 dòng chưa có khối lượng: dòng 4. Không định giá được dòng chưa có số — bổ sung khối lượng rồi chạy lại kiểm tra." — nêu đúng số dòng thiếu và số thứ tự của chúng | VAL-11 |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-10 · BR-02 → UAT-21/-22 · BR-03 → UAT-19 · BR-04 → UAT-20/-24 · BR-05 → UAT-23 · BR-06 → UAT-25/-27 · BR-07 → UAT-28 · BR-08 → UAT-13 · BR-09 → UAT-8/-31 · BR-10 → UAT-29 · BR-11 → UAT-34/-35. Đủ 11/11.
- **Danh mục kiểm tra trước ký**: `chk_value` → UAT-11 · `chk_boq` → UAT-12 · `chk_payment` → UAT-13 · `chk_terms` → UAT-16 · `chk_partner` → UAT-14 · `chk_guarantee` → UAT-17 · `chk_effective` → UAT-18 · `chk_doc` → UAT-15 · `chk_owner` → UAT-19 · `chk_qty` → UAT-40. Đủ 10/10.
- **Kiểm tra dữ liệu & thông điệp**: VAL-1 → UAT-10 · VAL-2 → UAT-12 · VAL-3 → UAT-4 · VAL-4 → UAT-21 · VAL-5 → UAT-13 · VAL-6 → UAT-23 · VAL-7 → UAT-31 · VAL-8 → UAT-16 · VAL-9 → UAT-18 · VAL-10 → UAT-11 · VAL-11 → UAT-41. Đủ 11/11.
- **Kết quả mong đợi**: KQ-1 → UAT-4/-16 · KQ-2 → UAT-21/-22 · KQ-3 → UAT-5 · KQ-4 → UAT-23 · KQ-5 → UAT-25 · KQ-6 → UAT-28 · KQ-7 → UAT-29 · KQ-8 → UAT-30 · KQ-9 → UAT-32 · KQ-10 → UAT-34. Đủ 10/10.
- Mười hai kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1 → UAT-20 · -2 → UAT-23 · -3 → UAT-25 · -4 → UAT-28 · -5 → UAT-30 · -6 → UAT-8/-31 · -7 → UAT-34 · -8 → UAT-13 · -9 → UAT-10 · -10 → UAT-11 · -11 → UAT-16 · -12 → UAT-21.
- UAT-27 là kịch bản phá hoại có kiểm soát — chạy trên môi trường kiểm thử, không chạy trên dữ liệu thật.
- Liên màn hình: **BID-01** (mã gói thầu, bộ cột BOQ), **BID-03** (BOQ báo giá chốt, điều kiện thương mại), **PF-01** (hồ sơ đối tác), **PF-02** (quyền theo dự án), **PF-04** (ký duyệt, nhắc hạn), **PF-05** (phiên bản bản thảo HĐ), **ACC-02** (kho dự án), **ORD-01** (tạm ứng), **PRJ-01** (giá khoán), **RPT-02** (bảng kê hợp đồng).
