# BID-05 · Ký hợp đồng & khởi tạo dự án — Kịch bản kiểm thử

> Nguồn: `spec/BID-05.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS-HĐ` / `TS-DA` = bảng *Tham số — hợp đồng* / *— khởi tạo dự án* · `CHK` = bảng *Tham số — danh mục kiểm tra trước ký* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID05-1 | Gói thầu "Thương thảo hoàn thành" ở BID-03 | Mở màn hình hợp đồng | Có đủ các khối của mockup: kiểm tra hợp đồng, thông tin hợp đồng, hoàn thiện hợp đồng và điều kiện chốt (bước 4), thành phần dự án; sau chốt thêm bước 5, bước 6 và danh sách phụ lục *(đổi 12/11/2026)* | Mockup UI · CN-1…12 |
| UAT-BID05-2 | Hợp đồng soạn xong, `precheck` = chưa chạy | Xem khối kiểm tra hợp đồng | Liệt kê đủ 8 hạng mục kiểm tra kèm đối tượng đối chiếu, mức (Chặn / Cảnh báo) và các kết quả mà mục đó thực sự cho ra được | CHK · TS-HĐ · `precheck` |
| UAT-BID05-3 | Vừa chạy kiểm tra xong | Xem kết quả | Mỗi hạng mục hiện kết quả **kèm số liệu đối chiếu**, không chỉ Đạt / Không đạt suông | CN-1 |
| UAT-BID05-4 | Kết quả kiểm tra còn mục ở mức Cảnh báo chưa xác nhận; gói "Đang ký số", đủ điều kiện chốt | Xem nút **Chốt HĐ – Đã ký** | Nút còn khoá; mục cảnh báo phải được người kiểm tra xác nhận kèm lý do ngay tại dòng đó | CN-1 · KQ-1 · VAL-3 · BR-22 |
| UAT-BID05-6 | Đang khai thông tin hợp đồng | Nhập giá trị trước VAT và thuế suất | Giá trị sau VAT tính tự động | CN-4 · TS-HĐ · `contract_value` |
| UAT-BID05-8 | Chưa gán Giám đốc dự án | Mở khối nhân sự hiện trường | Khối bị khoá — phần phân công hiện trường chỉ mở sau khi đã gán Giám đốc dự án | BR-13 · CN-7 |
| UAT-BID05-42 | Gói thầu chốt tiến độ 01/09/2026 – 30/08/2027 và bảo hành 12 tháng từ ngày bàn giao đưa vào sử dụng | Mở form hợp đồng mới từ gói thầu đó | Bốn ô `contract_start`, `contract_end`, `warranty_months`, `warranty_from` đã điền sẵn đúng giá trị gói thầu; không ô nào chỉ đọc — sửa được khi thương thảo đổi điều kiện | KQ-11 · BR-16 · CN-4 · TS-HĐ · `contract_start`, `warranty_months` |
| UAT-BID05-43 | Tiếp UAT-BID05-42 | Sửa tiến độ thành 05/01/2026 – 20/12/2026 và bảo hành thành 24 tháng | Ba ô đã sửa hiện chú thích giá trị gốc ngay bên cạnh: "gói thầu: 01/09/2026", "gói thầu: 30/08/2027", "gói thầu: 12"; ô mốc tính không đổi ghi "như gói thầu" | KQ-12 · BR-17 · TS-HĐ · `tender_baseline` |
| UAT-BID05-44 | Gói thầu chỉ khai `exec_days` = 270, không có cặp mốc ngày | Mở form hợp đồng mới từ gói thầu đó | Hợp đồng nạp số ngày đó và đếm từ ngày hiệu lực hợp đồng để ra `contract_start` / `contract_end` | TS-HĐ · `contract_start`, `contract_end` |
| UAT-BID05-45 | Kiểm tra hợp đồng đã đạt, chưa chốt HĐ | Sửa một ô trong khối tiến độ thi công / bảo hành rồi lưu | Sửa ở khối này cũng làm kết quả kiểm tra hết hiệu lực như mọi nội dung khác của hợp đồng | CN-4 · BR-03 |
| UAT-BID05-46 | Gói "Hoàn tất" | Mở lại màn hình hợp đồng; sửa tiến độ; thử sửa giá trị | Thông tin chung sửa được, có lịch sử người sửa · thời điểm · cũ → mới, không chạy lại kiểm tra; giá trị, khối lượng, BOQ chỉ đọc — chỉ đổi qua phụ lục Loại 02 *(đổi 12/11/2026 · Q-270)* | BR-27 · VAL-26 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID05-10 | `precheck` = chưa chạy; gói "Đang ký số", đủ điều kiện chốt | Bấm **Chốt HĐ – Đã ký** | "Chạy kiểm tra hợp đồng trước khi chốt — Chốt HĐ – Đã ký khởi tạo dự án nên chỉ mở khi kết quả đạt." | VAL-1 · BR-01 · BR-22 |
| UAT-BID05-11 | Giá trị hợp đồng lệch quá 1% so với giá trị báo giá đã chốt ở BID-03 | Chạy kiểm tra hợp đồng | `chk_value` báo Không đạt (mức Chặn): "Giá trị hợp đồng lệch {x}% so với BOQ báo giá đã chốt. Kiểm tra lại trước khi khởi tạo dự án."; nút **Chốt HĐ – Đã ký** vẫn khoá | CHK · `chk_value` · VAL-10 |
| UAT-BID05-12 | BOQ hợp đồng còn dòng lệch khối lượng hoặc đơn giá so với BOQ báo giá đã chốt | Chạy kiểm tra | `chk_boq` báo Không đạt (mức Chặn); thông điệp tổng "Còn {n} mục chưa đạt: {danh sách}. Xử lý xong mới khởi tạo được dự án."; nút **Chốt HĐ – Đã ký** khoá | CHK · `chk_boq` · VAL-2 |
| UAT-BID05-14 | Đối tác tiếp nhận ở BID-01 mới có tên và mã số thuế, chưa khai địa chỉ đăng ký và người đại diện | Chạy kiểm tra | `chk_partner` báo **Chưa nhập** ở mức Chặn — mục này chỉ kiểm tra có/không nên không có trạng thái Không đạt; dòng chưa nhập mở thẳng form sửa hồ sơ pháp nhân của đối tác (quyền theo PF-01), người không có quyền được dẫn về hồ sơ gói thầu để chọn đối tác khác; nút **Chốt HĐ – Đã ký** vẫn khoá | CHK · `chk_partner` · BR-04 · BR-02 |
| UAT-BID05-15 | Báo giá đã chốt ở BID-03 không trỏ được tới bản BOQ nào | Chạy kiểm tra | `chk_doc` báo **Chưa nhập** ở mức Chặn — mục này không có trạng thái Không đạt; bình thường hiện `V{n}` theo bản BOQ của giá chốt | CHK · `chk_doc` · VAL-7 · BR-05 |
| UAT-BID05-16 | Điều khoản thanh toán khác điều kiện thương mại đã chốt ở BID-03 | Chạy kiểm tra, ghi lý do và xác nhận mục cảnh báo | `chk_terms` ở mức Cảnh báo: "Điều khoản thanh toán khác báo giá đã chốt ({nội dung cũ}). Xác nhận nếu đã thống nhất lại với đối tác."; xác nhận xong kết quả chuyển Đạt — một điều kiện của **Chốt HĐ – Đã ký** | CHK · `chk_terms` · VAL-9 · KQ-1 |
| UAT-BID05-22 | Kiểm tra đã đạt, chưa chốt | Lần lượt đổi một điều khoản, giá trị, tiến độ, % giữ lại; rồi đổi số HĐ, % được tạm ứng, hình thức bảo lãnh, PDF | Bốn thay đổi đầu đưa `precheck` về chưa chạy; bốn thay đổi sau không — chúng không nằm trong danh mục kiểm tra *(12/11/2026)* | BR-03 · KQ-2 |
| UAT-BID05-25 | Gói "Đang ký số", kiểm tra đạt, đủ điều kiện chốt, đã gán đủ nhân sự | Bấm **Chốt HĐ – Đã ký** | Gói "Đã ký - HĐ chốt"; sinh đồng thời tám kết quả: mã dự án, BOQ hợp đồng, danh mục hạng mục, kho dự án, danh sách thành phần, các thông báo vai trò, hồ sơ bản vẽ thiết kế và hồ sơ Shopdrawing — hai hồ sơ sau ở trạng thái rỗng; không sinh bảng đợt thanh toán | BR-07 · BR-22 · CN-6 · KQ-5 |
| UAT-BID05-26 | Gói thầu mang mã `KH_0112` | Xem mã dự án vừa sinh | Dự án lấy nguyên mã gói thầu của BID-01 — hợp đồng và dự án cùng một mã | TS-DA · `project_id` |
| UAT-BID05-27 | Mô phỏng lỗi ở một bước của quá trình khởi tạo | Bấm **Chốt HĐ – Đã ký** | Chạy trong một giao dịch — lỗi ở bất kỳ bước nào thì hoàn tác toàn bộ, không để dự án sinh ra thiếu bộ phận | BR-08 |
| UAT-BID05-28 | Dự án vừa khởi tạo | Mở BOQ hợp đồng, thử sửa khối lượng và đơn giá | Cả hai khoá vĩnh viễn; thay đổi chỉ qua phụ lục HĐ Loại 02 hoặc làm lại lần mới, giữ nguyên bản gốc để đối chiếu | BR-08 · BR-27 · KQ-6 · TS-DA · `contract_boq[]` |
| UAT-BID05-29 | Tiếp UAT-BID05-25 | Kiểm tra thông báo của từng người được gán | Mỗi người nhận đúng một thông báo nêu tên dự án và **vai trò của chính họ**; không có thông báo chung gửi cả nhóm | BR-14 · KQ-7 |
| UAT-BID05-30 | Vừa được gán vào dự án với một vai trò | Người đó đăng nhập | Tự động có quyền xem dữ liệu của dự án theo PF-02, không cần Admin cấp riêng | KQ-8 |
| UAT-BID05-31 | Đã gán Giám đốc dự án | Giám đốc dự án phân công chỉ huy trưởng, cán bộ kỹ thuật, thủ kho công trình theo hạng mục | Gán nhân sự hai tầng: P. Đấu thầu gán thành phần dự án, Giám đốc dự án — sau khi được gán — mới phân công nhân sự hiện trường; chưa gán GĐ dự án thì báo "Gán Giám đốc dự án trước — người này sẽ phân công nhân sự hiện trường." | BR-13 · VAL-9 · CN-6/-7 · TS-DA · `members[]`, `site_team[]` |
| UAT-BID05-32 | Dự án vừa khởi tạo | Mở danh sách công trình | Dự án xuất hiện ngay với tiến độ 0% và đủ 4 tab | KQ-11 |
| UAT-BID05-33 | Tiếp UAT-BID05-25 | Mở danh mục kho (ACC-02) | Có một kho dự án sinh tự động khi khởi tạo, mã suy từ mã dự án | TS-DA · `project_wh` |
| UAT-BID05-36 | Hợp đồng gần nhất ở Khánh Hoà là `KH_0113`; có hợp đồng cũ mang mã `HD-YYYY-NNN` | Lưu hợp đồng mới ở Khánh Hoà | Số hợp đồng là `KH_0114`; hợp đồng cũ theo quy ước cũ giữ nguyên, không đánh số lại | TS-HĐ · `contract_no` |
| UAT-BID05-37 | Một công trình ở Quảng Ninh; một công trình không rút được địa điểm | Lưu hai hợp đồng | Tiền tố lần lượt là `MC` (lịch sử theo sổ) và `NL` | TS-HĐ · `contract_no` |
| UAT-BID05-38 | Sổ hợp đồng của một tiền tố có khoảng trống và có mã dùng lại cho phát sinh | Lưu hợp đồng mới | Số cấp là số lớn nhất đang dùng cộng một, không đếm theo số dòng | TS-HĐ · `contract_no` |
| UAT-BID05-39 | Dự án vừa khởi tạo | Mở danh mục hạng mục của dự án | WBS sinh từ mã hạng mục của BOQ hợp đồng; vùng miền suy ra từ địa điểm công trình | TS-DA · `wbs[]`, `region_stat` |
| UAT-BID05-40 | Hợp đồng soạn xong; BOQ hợp đồng 5 dòng, riêng dòng 4 để trống khối lượng — trạng thái hợp lệ mang sang từ BID-01 kèm ghi chú "chờ làm rõ" | Chạy kiểm tra hợp đồng | `chk_qty` báo **Chưa nhập** ở mức Chặn và nêu dòng 4; nút **Chốt HĐ – Đã ký** khoá vì danh mục còn mục mức Chặn | CHK · `chk_qty` · VAL-12 |
| UAT-BID05-41 | Tiếp UAT-BID05-40 | Mở chi tiết mục `chk_qty` trong khối kiểm tra trước ký | "BOQ hợp đồng còn 1 dòng chưa có khối lượng: dòng 4. Không định giá được dòng chưa có số — bổ sung khối lượng rồi chạy lại kiểm tra." — nêu đúng số dòng thiếu và số thứ tự của chúng | VAL-14 |
| UAT-BID05-47 | Tiếp UAT-BID05-43 — hợp đồng ghi bảo hành 24 tháng, `tender_baseline` chụp 12 tháng | Chạy kiểm tra hợp đồng, rồi ghi lý do và xác nhận | `chk_warranty` báo **Không đạt** ở mức Cảnh báo: "{Tên điều kiện} trong hợp đồng là {giá trị mới}, gói thầu chốt {giá trị cũ}. Ghi lý do và xác nhận nếu đã thương thảo lại."; mục gom cả ba ô đã đổi thành một cảnh báo; xác nhận từng điều kiện xong thì chuyển Đạt — một điều kiện của **Chốt HĐ – Đã ký** | CHK · `chk_warranty` · VAL-16 · BR-17 · KQ-12 |
| UAT-BID05-48 | Hợp đồng khai `warranty_months` = 24 nhưng bỏ trống mốc tính | Chạy kiểm tra hợp đồng | `chk_warranty` báo **Chưa nhập**: "Chọn mốc bắt đầu tính bảo hành — không có mốc thì không tính được ngày hết hạn để nhắc lập hồ sơ thanh toán phần giá trị giữ lại." | CHK · `chk_warranty` · VAL-15 · TS-HĐ · `warranty_from` |
| UAT-BID05-49 | Đang khai thời hạn bảo hành của hợp đồng | Nhập 150 tháng, rồi nhập 12,5 tháng | Cả hai bị chặn — `warranty_months` là số nguyên trong khoảng 0–120 | TS-HĐ · `warranty_months` |
| UAT-BID05-51 | Tiếp UAT-BID05-47, hợp đồng đã ký đủ và dự án đã khởi tạo ba tháng | Mở lại hợp đồng | `tender_baseline` giữ nguyên bản chụp của gói thầu: các ô đã đổi vẫn hiện "gói thầu: {giá trị cũ}", trả lời được câu "điều kiện này có phải thứ đã dùng để dựng giá không" | BR-17 · TS-HĐ · `tender_baseline` |
| UAT-BID05-52 | Tiếp UAT-BID05-51 | Mở bảng theo dõi hợp đồng ở RPT-05 | RPT-05 nhận `contract_start`, `contract_end` và `warranty_end_date` (24 tháng kể từ mốc đã chọn) từ hợp đồng này; cột tiến độ theo hợp đồng đặt cạnh cột tiến độ thi công thực tế dựng từ sản lượng PRJ-02, số thực tế không bao giờ ghi ngược lại vào hợp đồng | KQ-13 · BR-18 |
| UAT-BID05-53 | Hợp đồng chưa khai giá trị, và một hợp đồng khác đã khai nhưng lệch 3% so với BOQ chốt | Chạy kiểm tra hợp đồng cho từng hợp đồng | Trường hợp đầu `chk_value` báo **Chưa nhập**: "Chưa khai giá trị hợp đồng. Nhập giá trị trước VAT rồi chạy lại kiểm tra."; trường hợp sau báo **Không đạt**. Hai nhãn khác nhau vì một bên phải đi nhập liệu, một bên phải đi sửa sai lệch; với mức Chặn thì cả hai đều giữ nút **Chốt HĐ – Đã ký** khoá như nhau | BR-02 · CHK · `chk_value` · VAL-13 |

## C · Ký số, chốt HĐ, bước 5 · 6, phụ lục HĐ *(12/11/2026)*

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID05-57 | Gói "Thương thảo hoàn thành", hợp đồng soạn xong | Bấm **Bắt đầu ký** | Gói và hợp đồng "Đang ký số"; không có tệp nào được gửi đi ký; lịch sử ghi người bấm, thời điểm | BR-21 · KQ-16 · Q-265 |
| UAT-BID05-58 | Gói "Đang thương thảo (lần 2)" | Mở hợp đồng, tìm nút **Bắt đầu ký** | Nút khoá: "Gói đang ở Đang thương thảo (lần 2). Thông báo thương thảo hoàn thành ở BID-03 trước khi bắt đầu ký." | VAL-18 · BR-21 |
| UAT-BID05-59 | Tiếp UAT-57, kiểm tra đạt; đã nhập số HĐ, tổng giá trị 12.480.000.000, % tạm ứng 15, chọn Thư bảo lãnh, chưa tải PDF | Xem khung Điều kiện chốt, bấm **Chốt HĐ – Đã ký** | Bốn ✓, PDF ✕; nút khoá: "Còn thiếu File PDF hợp đồng đã ký — đủ thì mới chốt được hợp đồng." | BR-22 · VAL-19 · KQ-17 · CN-8 |
| UAT-BID05-60 | Tiếp UAT-59 | Tải tệp DOCX, rồi tải PDF; bấm chốt | DOCX bị từ chối "Tải bản PDF hợp đồng đã ký."; PDF nhận. Gói "Đã ký - HĐ chốt"; dự án, BOQ HĐ, kho, hạng mục sinh như trước; người được gán nhận thông báo vai trò; không phòng ban nào nhận thông báo "HĐ đã chốt"; bước 5 và bước 6 cùng mở; ngày ký mặc định hôm nay | BR-22 · BR-12 · BR-25 · VAL-21 · KQ-18 · Q-266 |
| UAT-BID05-61 | Gói chưa "Đang ký số" (còn "Thương thảo hoàn thành"), đủ mọi điều kiện | Gọi thao tác **Chốt HĐ – Đã ký** | "Bấm Bắt đầu ký trước — hợp đồng chỉ chốt được khi đang ký số." | VAL-22 · BR-22 |
| UAT-BID05-62 | Mô phỏng lỗi ở bước sinh kho khi bấm chốt | Bấm **Chốt HĐ – Đã ký** | Hoàn tác cả việc chốt; gói vẫn "Đang ký số", không có dự án nửa vời | BR-22 · BR-07 |
| UAT-BID05-63 | Form hoàn thiện hợp đồng | Nhập % được tạm ứng 120, rồi −5, rồi 0 | 120 và −5 bị chặn "% được tạm ứng HĐ phải từ 0 đến 100."; 0 lưu được; ô % thu hồi tạm ứng không đổi | VAL-20 · BR-23 · TS-HĐ · `advance_pct` |
| UAT-BID05-64 | Gói "Đang ký số" | Mở bước 5 | Bị chặn: "Mã vật tư theo HĐ chỉ tạo khi hợp đồng đã chốt." | VAL-23 · BR-24 |
| UAT-BID05-65 | Gói "Đã ký - HĐ chốt", 18 dòng HĐ, 12 dòng đã có mã | Ở bước 5 bấm **Tạo mã** dòng 1.2, lưu mã mới | Mã mới có trong danh mục mã vật tư chung, gắn hợp đồng và dự án; bước 5 hiện 13/18 | BR-24 · KQ-19 · CN-9 · Q-269 |
| UAT-BID05-66 | Tiếp UAT-65 | Bấm **Hoàn thành tạo mã** | Hỏi "Còn 5/18 dòng chưa có mã vật tư. Vẫn đánh dấu hoàn thành bước 5?"; xác nhận thì cờ bước 5 bật; gói vẫn "Đã ký - HĐ chốt", tiến độ 1/2 | BR-24 · BR-26 · VAL-24 · Q-268 |
| UAT-BID05-67 | Tiếp UAT-60, DS gói: P.TQT, P.Kế toán, P.Vật tư; bước 5 chưa xong | Ở bước 6 bấm **Gửi thông báo**, thêm P.Thi công, tích "Lưu vào danh sách của gói", gửi | Bốn phòng ban nhận qua hộp thư và thông báo đẩy; DS của gói có thêm P.Thi công; cờ bước 6 bật — không chờ bước 5; bỏ hết người nhận thì "Chọn ít nhất một phòng ban nhận thông báo." | BR-25 · VAL-25 · KQ-20 · CN-10 · Q-267 · Q-273 |
| UAT-BID05-68 | Tiếp UAT-66 và UAT-67 | Mở gói | Gói tự sang "Hoàn tất", lịch sử ghi người thực hiện "Hệ thống" | BR-26 · KQ-21 |
| UAT-BID05-69 | Tiếp UAT-67 | Bấm **Gửi lại** cho P.Vật tư | P.Vật tư nhận thêm một thông báo; lịch sử ghi lần gửi lại; cờ bước 6 không đổi | BR-25 · CN-10 |
| UAT-BID05-70 | Gói "Hoàn tất" | Sửa tiến độ thi công và DS phòng ban; thử sửa giá trị hợp đồng | Tiến độ, DS lưu được, không chạy lại kiểm tra, lịch sử ghi cũ → mới; sửa giá trị bị chặn "Hợp đồng đã chốt. Giá trị, khối lượng chỉ đổi bằng phụ lục HĐ Loại 02." | BR-27 · VAL-26 · KQ-22 · Q-270 |
| UAT-BID05-71 | Gói "Hoàn tất", không có phụ lục mở | Thêm phụ lục bỏ trống loại; rồi chọn Loại 01 không ghi thay đổi nào | Lần lượt: "Chọn loại phụ lục (01 hoặc 02) và ghi lý do."; "Ghi ít nhất một nội dung thay đổi của phụ lục Loại 01." | VAL-27 · VAL-28 · BR-28 · BR-29 |
| UAT-BID05-72 | Tiếp UAT-71 | Phụ lục Loại 01 "Đổi tiến độ hoàn thành → 31/03" (hiện ngay "Đang ký số PLHĐ", không có nút Bắt đầu ký — Q-289), nhập số phụ lục, ngày ký, PDF, **Ký phụ lục** | Không có BOQ, thẩm định hay áp giá; ký xong `contract_end` = 31/03 và hiện ở RPT-05, lịch sử ghi kèm mã phụ lục; việc mã vật tư tự xong; gửi thông báo phòng ban thì phụ lục "Hoàn tất"; giá trị hợp đồng không đổi | BR-29 · BR-31 · BR-32 · KQ-23 · KQ-24 |
| UAT-BID05-73 | Gói có lần thương thảo 1 kết quả 3b "đá sảnh phụ để sau" | Thêm phụ lục Loại 02, chọn lần 1 làm căn cứ, thêm dòng mới "Ốp đá sảnh phụ" 400 m² | Không có phiếu thẩm định BID-02; tóm lược lần 1 hiện trong phụ lục; phụ lục sang thương thảo ở BID-03, lần đánh từ 1 | BR-30 · KQ-23 · Q-271 · Q-272 |
| UAT-BID05-74 | Phụ lục Loại 02 đổi khối lượng dòng gắn hạng mục HĐ, cộng một dòng mới chưa có đơn giá | Bấm **Bỏ qua**; nhập đơn giá dòng mới rồi bấm lại | Lần đầu "Còn 1 dòng hạng mục mới chưa có đơn giá…"; lần sau phụ lục sang ký, không qua BID-03 | BR-30 · VAL-29 |
| UAT-BID05-75 | Phụ lục "Đang ký số PLHĐ", đã có ngày ký | Bấm **Ký phụ lục** khi chưa có số phụ lục và PDF | "Nhập số phụ lục và tải PDF phụ lục đã ký trước khi ký." | BR-31 · VAL-30 |
| UAT-BID05-76 | Phụ lục Loại 02 có 2 dòng mới, 1 dòng đã chọn mã có sẵn | Ký phụ lục; mở việc mã vật tư | Chỉ dòng mới còn lại cần tạo mã; dòng gắn hạng mục HĐ không hiện; thông báo phòng ban mở song song; xong cả hai thì phụ lục "Hoàn tất"; gói giữ "Hoàn tất" | BR-32 · BR-19 · KQ-24 |
| UAT-BID05-77 | Hợp đồng có PL01 "Hoàn tất", PL02 "Đang thương thảo PLHĐ (lần 1)" | Mở khung phụ lục; bấm **Thêm phụ lục** | Danh sách hiện cả hai phụ lục với loại, nội dung, giá trị, trạng thái; thêm phụ lục bị chặn "Phụ lục KH_0114-PL02 đang ở Đang thương thảo PLHĐ (lần 1)…" | BR-33 · VAL-31 · KQ-25 · CN-12 |
| UAT-BID05-78 | Gói đã ký hợp đồng trước ngày chuyển dữ liệu | Mở gói sau khi chuyển | "Hoàn tất", cờ bước 5 và 6 đều bật, người thực hiện "Hệ thống"; % tạm ứng, bảo lãnh, PDF để trống và sửa được | BR-34 · Q-275 |
| UAT-BID05-79 | Gói chuyển từ "Làm trước HĐ", đã có dự án cùng mã, nay đi nhánh A và thương thảo hoàn thành | Bắt đầu ký, đủ điều kiện, **Chốt HĐ – Đã ký** | Không sinh dự án thứ hai; hợp đồng, BOQ HĐ, hạng mục gắn vào dự án cùng mã; Đơn hàng, kho giữ nguyên | BR-20 · Q-276 |
| UAT-BID05-80 | Gói đã ký lần 1, "Hoàn tất"; lần 2 thương thảo hoàn thành | Bắt đầu ký, chốt "Ký lại hợp đồng (lần 2)" | Không có dự án mới; bước 5 mở lại cho dòng BOQ mới chưa có mã, bước 6 mở lại; gói "Hoàn tất" khi đủ cả hai | BR-17 |

## D · Kịch bản đã bỏ *(12/11/2026)*

Các kịch bản dưới đây kiểm hành vi đặc tả đã bỏ trước hoặc trong đợt 12/11/2026; giữ mã để không dùng lại.

| Mã | Lý do bỏ |
|---|---|
| UAT-BID05-5 | chuỗi ký số trong hệ thống đã bỏ (19/10/2026); ký ngoài hệ thống, chỉ còn trạng thái "Đang ký số" (Q-265) — thay bằng UAT-57 |
| UAT-BID05-7 | bảng đợt thanh toán không còn trên hợp đồng (19/10/2026) |
| UAT-BID05-9 | không còn nút gửi nhắc ký (Q-265) |
| UAT-BID05-13 | `chk_payment` và bảng đợt thanh toán đã bỏ |
| UAT-BID05-17 | `chk_guarantee` đã bỏ; hình thức bảo lãnh nay là điều kiện chốt — thay bằng UAT-59 |
| UAT-BID05-18 | `chk_effective` và cặp ngày hiệu lực đã bỏ |
| UAT-BID05-19 | không còn ký số bước 1 của TP Đấu thầu; `chk_owner` giữ, luôn Đạt |
| UAT-BID05-20 | chuỗi ký nội bộ tuần tự đã bỏ |
| UAT-BID05-21 | không còn chữ ký trong hệ thống để huỷ; sửa sau khi kiểm tra đạt chỉ đưa `precheck` về chưa chạy (UAT-22) |
| UAT-BID05-23 | "chờ ký đủ" thay bằng điều kiện chốt (PDF HĐ đã ký) — thay bằng UAT-59 |
| UAT-BID05-24 | hệ thống không theo dõi chữ ký đối tác; ngày ký nhập ở bước 4 — thay bằng UAT-57/-60 |
| UAT-BID05-34 | `guarantees[]` và nhắc gia hạn bảo lãnh đã bỏ |
| UAT-BID05-35 | như UAT-34 |
| UAT-BID05-50 | cặp ngày hiệu lực hợp đồng đã bỏ |
| UAT-BID05-54 | bảng đợt thanh toán đã bỏ |
| UAT-BID05-55 | như UAT-54 |
| UAT-BID05-56 | như UAT-54 |

## Ghi chú bao phủ

- *(bản trước 19/10/2026 — mã số chưa đánh lại, xem dòng cuối)* **Quy tắc nghiệp vụ**: BR-01 → UAT-10 · BR-02 → UAT-14/-15/-17/-53 · BR-03 → UAT-21/-22/-45 · BR-04 → UAT-14 · BR-05 → UAT-19 · BR-06 → UAT-20/-24 · BR-07 → UAT-23 · BR-08 → UAT-25/-27 · BR-09 → UAT-28 · BR-10 → UAT-13 · BR-11 → UAT-46/-54/-55 · BR-12 → UAT-56 · BR-13 → UAT-8/-31 · BR-14 → UAT-29 · BR-15 → UAT-34/-35 · BR-16 → UAT-42/-44 · BR-17 → UAT-43/-47/-51 · BR-18 → UAT-50/-52.
- *(bản trước 19/10/2026 — mã số chưa đánh lại, xem dòng cuối)* **Danh mục kiểm tra trước ký**: `chk_value` → UAT-11/-53 · `chk_boq` → UAT-12 · `chk_payment` → UAT-13 · `chk_terms` → UAT-16 · `chk_partner` → UAT-14 · `chk_guarantee` → UAT-17 · `chk_effective` → UAT-18 · `chk_warranty` → UAT-47/-48 · `chk_doc` → UAT-15 · `chk_owner` → UAT-19 · `chk_qty` → UAT-40.
- *(bản trước 19/10/2026 — mã số chưa đánh lại, xem dòng cuối)* **Kiểm tra dữ liệu & thông điệp**: VAL-1 → UAT-10 · VAL-2 → UAT-12 · VAL-3 → UAT-4 · VAL-4 → UAT-21 · VAL-5 → UAT-13 · VAL-6 → UAT-55 · VAL-7 → UAT-56 · VAL-8 → UAT-23 · VAL-9 → UAT-31 · VAL-10 → UAT-16 · VAL-11 → UAT-18 · VAL-12 → UAT-11 · VAL-13 → UAT-53 · VAL-14 → UAT-41 · VAL-15 → UAT-48 · VAL-16 → UAT-47 · VAL-17 → UAT-50.
- *(bản trước 19/10/2026 — mã số chưa đánh lại, xem dòng cuối)* **Kết quả mong đợi**: KQ-1 → UAT-4/-16 · KQ-2 → UAT-21/-22 · KQ-3 → UAT-5 · KQ-4 → UAT-23 · KQ-5 → UAT-25 · KQ-6 → UAT-28 · KQ-7 → UAT-29 · KQ-8 → UAT-30 · KQ-9 → UAT-32 · KQ-10 → UAT-34 · KQ-11 → UAT-42 · KQ-12 → UAT-43/-47 · KQ-13 → UAT-52.
- **Tiến độ, bảo hành và bản chụp gói thầu** (YC-06): `contract_start` / `contract_end` → UAT-42/-44/-50/-52 · `warranty_months` / `warranty_from` → UAT-42/-48/-49 · `tender_baseline` → UAT-43/-47/-51. Bốn ô nạp sẵn nhưng vẫn sửa được; mỗi ô đã sửa hiện "gói thầu: {giá trị cũ}" và mục `chk_warranty` gom chúng thành một cảnh báo.
- *(bản trước 19/10/2026 — mã số chưa đánh lại, xem dòng cuối)* Mười tám kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1 → UAT-20 · -2 → UAT-23 · -3 → UAT-25 · -4 → UAT-28 · -5 → UAT-30 · -6 → UAT-8/-31 · -7 → UAT-34 · -8 → UAT-13 · -9 → UAT-10 · -10 → UAT-11 · -11 → UAT-16 · -12 → UAT-21 · -13 → UAT-14 · -14 → UAT-54 · -15 → UAT-56 · -16 → UAT-42 · -17 → UAT-43/-47 · -18 → UAT-52.
- **Đợt 12/11/2026 (Q-256, Q-265 – Q-273, Q-275, Q-276)**: BR-21 → UAT-57/-58 · BR-22 → UAT-4/-10/-25/-59/-60/-61/-62 · BR-23 → UAT-63 · BR-24 → UAT-64/-65/-66 · BR-25 → UAT-60/-67/-69 · BR-26 → UAT-66/-68 · BR-27 → UAT-28/-46/-70 · BR-28 → UAT-71 · BR-29 → UAT-71/-72 · BR-30 → UAT-73/-74 · BR-31 → UAT-72/-75 · BR-32 → UAT-72/-76 · BR-33 → UAT-77 · BR-34 → UAT-78 · BR-20 (viết lại) → UAT-79 · BR-17 (bổ sung) → UAT-80 · VAL-18 → UAT-58 · VAL-19 → UAT-59 · VAL-20 → UAT-63 · VAL-21 → UAT-60 · VAL-22 → UAT-61 · VAL-23 → UAT-64 · VAL-24 → UAT-66 · VAL-25 → UAT-67 · VAL-26 → UAT-46/-70 · VAL-27/-28 → UAT-71 · VAL-29 → UAT-74 · VAL-30 → UAT-75 · VAL-31 → UAT-77 · KQ-16 → UAT-57 · KQ-17 → UAT-59 · KQ-18 → UAT-60 · KQ-19 → UAT-65 · KQ-20 → UAT-67 · KQ-21 → UAT-68 · KQ-22 → UAT-70 · KQ-23 → UAT-72/-73 · KQ-24 → UAT-72/-76 · KQ-25 → UAT-77. Kịch bản đặc tả -26 … -43 → UAT-57, -58, -59, -60, -63, -65, -66, -67, -68, -69, -70, -72, -73, -74, -75, -76, -77, -78.
- **Bộ này cũ hơn đặc tả hiện tại**: các dòng ánh xạ BR / CHK / VAL / KQ phía trên đợt 12/11/2026 viết theo bản có chuỗi ký số, 11 mục kiểm tra và bảng đợt thanh toán. Đợt này viết lại các kịch bản còn dùng được (UAT-1, -2, -4, -10, -11, -12, -15, -16, -22, -25, -28, -32, -40, -46) theo BID-05 hiện tại và chuyển các kịch bản không còn đối tượng sang mục D; các mã số trong những dòng ánh xạ cũ chưa được đánh lại.
- UAT-27 là kịch bản phá hoại có kiểm soát — chạy trên môi trường kiểm thử, không chạy trên dữ liệu thật.
- **Hồ sơ bản vẽ sinh khi khởi tạo dự án — đã khép**: BR-08 của BID-05 nay liệt kê chín kết quả, gồm hồ sơ bản vẽ thiết kế và hồ sơ Shopdrawing sinh rỗng (YC-07, YC-08). UAT-BID05-25 kiểm cả hai; nội dung bên trong hai hồ sơ vẫn thuộc **ORD-02** (UAT-ORD02-13 / -18), không nhân bản ở đây.
- Liên màn hình: **BID-01** (mã gói thầu, bộ cột BOQ, điều kiện thực hiện của gói thầu), **BID-03** (BOQ báo giá chốt, điều kiện thương mại), **PF-01** (hồ sơ đối tác), **PF-02** (quyền theo dự án), **PF-04** (ký duyệt, nhắc hạn), **PF-05** (phiên bản bản thảo HĐ), **ACC-02** (kho dự án), **ORD-01** (tạm ứng, đợt thanh toán), **ORD-02** (hồ sơ bản vẽ sinh theo dự án), **PRJ-01** (giá khoán), **PRJ-02** (tiến độ thi công thực tế), **RPT-02** (bảng kê hợp đồng), **RPT-05** (tiến độ hợp đồng, hạn bảo hành).
