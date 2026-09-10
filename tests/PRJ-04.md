# PRJ-04 · Xuất nhập kho dự án — Kịch bản kiểm thử

> Nguồn: `spec/PRJ-04.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.
>
> Kho đặt tại công trình, do dự án tự quản — khác kho tổng/kho nhà máy ở ACC-02. Mẫu hai chữ ký (GĐ dự án → Kế toán trưởng) giống ACC-04; xuất kho là thời điểm vật tư thành chi phí.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-PRJ04-1 | Kho công trình `DA-2026-014`, giá trị tồn 1,84 tỷ | Mở màn hình Kho › Kho dự án | Bảng tồn kho hiện đủ 3 cột cho từng vật tư: Tồn / Đang giữ / Khả dụng; cảnh báo dưới định mức như kho khác | CN-1 |
| UAT-PRJ04-2 | Đang lập phiếu xuất `PXDA-014-0188` | Chọn Hạng mục và Tổ nhận | Cả hai ô đều bắt buộc (✱); không lưu được khi thiếu một trong hai | TS · `wbs_id`/`crew_id` · CN-2 · BR-01 |
| UAT-PRJ04-3 | Kho tồn 420 m², đang giữ 300 (khả dụng 120) | Nhập số lượng xuất 150 m² | Bị chặn tại chỗ: "Chỉ còn 120 m² khả dụng (420 tồn, 300 đang giữ). Giảm số lượng hoặc chờ nhập thêm." | VAL-2 · BR-03 |
| UAT-PRJ04-4 | Phiếu xuất đang ở "chờ duyệt", GĐ dự án chưa ký | Kế toán trưởng bấm duyệt | Bị chặn; tồn và "đang giữ" không đổi | BR-04 |
| UAT-PRJ04-5 | GĐ dự án đã ký phiếu xuất 300 m² (chữ ký 1/2) | Xem tồn kho | "Đang giữ" CHƯA tăng — chỉ tăng sau chữ ký thứ hai của Kế toán trưởng | BR-05 · kịch bản gốc UAT-PRJ04-10 |
| UAT-PRJ04-6 | Kế toán trưởng mở phiếu xuất đã duyệt (đủ 2 chữ ký) | Thử sửa ô giá xuất kho | Ô chỉ đọc; gợi ý dùng bút toán điều chỉnh ở ACC-08 | TS · `unit_cost` · BR-02 |
| UAT-PRJ04-7 | Tổ trưởng ký nhận trên ứng dụng | In phiếu xuất | Phiếu hiện dấu ký điện tử kèm người, thời điểm, thiết bị — thay cho ô chữ ký tay | BR-06 · KQ-3 |
| UAT-PRJ04-8 | Dự án quyết toán xong, kho còn 40 m² | Bấm "Đóng kho" | Bị chặn kèm ba lựa chọn xử lý tồn (điều chuyển / hoàn nhập / hồ sơ thanh toán vật tư); chỉ đóng được khi tồn = 0 | VAL-5 · BR-10 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-PRJ04-9 | Phiếu xuất 300 m² được duyệt đủ 2 chữ ký | Xem tồn kho | Tồn giữ nguyên, "đang giữ" tăng 300, khả dụng giảm 300 | BR-05 · KQ-6 · kịch bản gốc UAT-PRJ04-3/-9 |
| UAT-PRJ04-10 | Phiếu xuất 513,6 triệu cho HM-01, tổ TD-004, vừa được duyệt và giao | Mở sổ chi phí công trình (ACC-09) và bảng khấu trừ VTTB của tổ (PRJ-03) | Chi phí 513,6 triệu vào hạng mục HM-01 trong sổ chi phí công trình; đồng thời bảng khấu trừ VTTB của TD-004 tăng đúng 513,6 triệu — hai đích không cộng trùng thành chi phí kép | BR-07 · BR-08 · KQ-4 · KQ-5 · kịch bản gốc UAT-PRJ04-5 |
| UAT-PRJ04-11 | Kiểm kê thiếu 12 m², định mức hao hụt cho phép 5 m² | Lưu kết quả kiểm kê không kèm biên bản | Bị chặn: "Chênh lệch 12 vượt định mức hao hụt 5. Cần biên bản quy trách nhiệm trước khi ghi sổ." | VAL-6 · BR-09 · kịch bản gốc UAT-PRJ04-6/-11 |
| UAT-PRJ04-12 | Tiếp UAT-PRJ04-11, đã đính biên bản quy trách nhiệm | Lưu lại | Bút toán xử lý thiếu được sinh, đưa số tồn về đúng số đếm thực tế, giá trị phần thiếu đẩy sang sổ chi phí công trình | BR-09 · KQ-7 |
| UAT-PRJ04-13 | Vật tư có định mức hao hụt 0,5%; sổ 1.000, đếm 988 (thiếu 12, định mức cho phép 5) | Lưu xử lý chênh lệch | Bị chặn tương tự UAT-11 — định mức tính theo phần trăm số sổ sách (5 = 0,5% × 1.000) | VAL-6 · BR-09 |
| UAT-PRJ04-14 | Nhập kho nguồn "cấp hàng NM/CƯ" ghi 300 m², thủ kho công trình đếm thực nhận 295 m² | Lập phiếu nhập kho dự án | Bị chặn cho tới khi ghi nhận sai lệch kèm ảnh: "Số nhận lệch 5 so với phiếu {mã}. Ghi nhận sai lệch kèm ảnh." | VAL-3 · TS · `qty_actual` |
| UAT-PRJ04-15 | Dự án quyết toán, kho còn 20 m² | Lập phiếu xử lý tồn "điều chuyển" sang dự án khác và ghi sổ | Tồn giảm 20 theo giá bình quân, không sinh chi phí; dự án nhận tự động có phiếu nhập "điều chuyển từ dự án khác" đúng giá vốn | BR-10 · KQ-8 · kịch bản gốc UAT-PRJ04-12 |
| UAT-PRJ04-16 | Vật tư tồn cuối dự án 64 thùng keo chà ron | Đưa vào hồ sơ thanh toán vật tư ở ORD-08 | Phiếu xử lý tồn (`XLT-<project>-NNNN`) ghi sổ theo giá bình quân, trừ vào tồn; ORD-08 nhận đúng số lượng và giá trị | BR-10 · KQ-9 |
| UAT-PRJ04-17 | Phiếu xuất 300 m² lập đủ thông tin (Hạng mục + Tổ nhận) | Bỏ trống ô Hạng mục (khác kịch bản bỏ trống Tổ nhận ở nhóm A) rồi Lưu | Chặn lưu kèm thông điệp bắt buộc — cả hai trường được kiểm độc lập, không chỉ kiểm một trường | VAL-1 · BR-01 · kịch bản gốc UAT-PRJ04-2/-13 |
| UAT-PRJ04-18 | Sau phiếu xuất, tồn kho đá granite giảm xuống dưới định mức tối thiểu | Xem cảnh báo | "Sau phiếu này {vật tư} còn {số}, dưới định mức {số}. Đã báo P. Cung ứng." | VAL-4 |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-2, UAT-17 · BR-02 → UAT-6 · BR-03 → UAT-3 · BR-04 → UAT-4 · BR-05 → UAT-5, UAT-9 · BR-06 → UAT-7 · BR-07 → UAT-10 · BR-08 → UAT-10 · BR-09 → UAT-11, UAT-12, UAT-13 · BR-10 → UAT-8, UAT-15, UAT-16. Đủ 10/10.
- **Kết quả mong đợi**: KQ-1 → UAT-2, UAT-17 · KQ-2 → UAT-6 · KQ-3 → UAT-7 · KQ-4 → UAT-10 · KQ-5 → UAT-10 · KQ-6 → UAT-1, UAT-9 · KQ-7 → UAT-11, UAT-12 · KQ-8 → UAT-8, UAT-15 · KQ-9 → UAT-15, UAT-16. Đủ 9/9.
- Mười hai kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1 → UAT-3 · -2 → UAT-2/-17 · -3 → UAT-9 · -4 → UAT-7 · -5 → UAT-10 · -6 → UAT-11 · -7 → UAT-8 · -8 → UAT-6 · -9 → UAT-4 · -10 → UAT-5 · -11 → UAT-13 · -12 → UAT-15.
- Đặc tả không nói rõ **ai duyệt "điều chuyển từ dự án khác"** ở phía dự án nhận — phiếu nhập tự sinh có cần một bước xác nhận riêng của thủ kho dự án nhận hay tự động ở trạng thái đã xác nhận; ghi khoảng trống ở `PL-C`.
- Đặc tả không định nghĩa **định mức hao hụt** được cấu hình ở đâu (theo vật tư toàn công ty hay theo từng dự án) — chỉ nói "khai theo vật tư (phần trăm của số sổ sách)".
- Liên màn hình: **ACC-02** (hệ thống kho, phân biệt với kho tổng/kho nhà máy), **ACC-04** (mẫu duyệt xuất & giá xuất kho dùng chung), **ORD-05** (cấp hàng từ NM/CƯ là một nguồn nhập), **PRJ-03** (khấu trừ VTTB tiêu thụ phiếu xuất), **ACC-09** (sổ chi phí công trình nhận chi phí từ phiếu xuất), **ORD-08** (hồ sơ thanh toán vật tư nhận tồn cuối).
