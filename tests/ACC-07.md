# ACC-07 · Thu chi — quỹ & ngân hàng — Kịch bản kiểm thử

> Nguồn: `spec/ACC-07.html`. Bao phủ quỹ là tài khoản chi tiết dưới 111 / 112 (yêu cầu 02/11/2026), sổ quỹ và số dư đọc từ sổ cái, không chi vượt số dư quỹ kể cả tài khoản ngân hàng, sổ tiền gửi lọc theo tài khoản, phiếu chi phải có đề nghị, đối chiếu báo có, nhập sao kê hàng loạt. Bộ kịch bản này đánh số riêng, không trùng số với bảng *Kịch bản kiểm thử* của trang HTML.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC07-1 | Menu **Tài chính → Sổ quỹ & ngân hàng** | Chọn lần lượt 1111 Tiền mặt - VP / 1112 Nhà máy / 1113 Quỹ phạt / 1121A01 TCB / 1121A02 VCB | Mỗi tài khoản quỹ có sổ riêng; KPI số dư đầu kỳ / thu / chi / cuối kỳ và bảng chứng từ đổi theo, tính độc lập; không có tab **Danh mục quỹ** |
| UAT-ACC07-2 | Mở sổ tiền gửi 1121A01 | Dùng bộ lọc | Lọc được theo tài khoản hạch toán đối ứng; không còn bộ lọc ngân hàng, số tài khoản ngân hàng (bỏ 02/11/2026 — PL-C Q-166) |
| UAT-ACC07-3 | Bấm **+ Phiếu chi** | Xem form | Trường **Chứng từ nguồn** bắt buộc; chọn đề nghị thanh toán đã duyệt thì số tiền mặc định bằng giá trị đề nghị, không cho nhập vượt |
| UAT-ACC07-4 | Có báo có ngân hàng chưa đối chiếu | Mở tab **Báo có chờ đối chiếu** | Mỗi dòng hiển thị "Hồ sơ khớp gợi ý" kèm nút "Xác nhận khớp" / "Xem" |
| UAT-ACC07-5 | Có tệp sao kê ngân hàng | Bấm **Nhập sao kê**, tải tệp | Bảng kết quả: phần khớp tự động được đánh dấu; phần còn lại chuyển tab "Chờ đối chiếu" |
| UAT-ACC07-6 | Tài khoản 1111 dư 1,74 tỷ | Lập phiếu chi tiền mặt 2 tỷ | Thông điệp "Quỹ {tên} còn {số dư}. Không chi vượt số dư tiền mặt."; nút ghi sổ không thực thi |
| UAT-ACC07-7 | Đang xem sổ quỹ tiền mặt | Bấm **Xuất Excel** | Tệp theo kỳ, đủ chiều phân tích: đối tượng, dự án, tài khoản hạch toán |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC07-8 | Tài khoản 1111 dư 1,74 tỷ | Lập phiếu chi 2 tỷ tiền mặt trên 1111 | Bị chặn kèm số dư hiện tại |
| UAT-ACC07-9 | Đề nghị `DNTT-2026-0271` được duyệt 1,84 tỷ | Lập ủy nhiệm chi 1,84 tỷ | Ghi sổ ngân hàng, giảm công nợ, cập nhật sổ chi tiết đối tác |
| UAT-ACC07-10 | Báo có 3,2 tỷ nội dung "COTECCONS TT DOT 4 HD 2026-008" | Mở tab đối chiếu | Gợi ý đúng `HSTU-2026-011`; xác nhận thì hồ sơ tự đóng, công nợ phải thu giảm — không nhập tay hai lần |
| UAT-ACC07-11 | Tệp sao kê 60 giao dịch | Tải lên | Khớp tự động phần rõ ràng; phần còn lại vào tab chờ đối chiếu, không tự đoán bừa |
| UAT-ACC07-12 | Khấu trừ phạt 8 triệu từ hồ sơ giao khoán (PRJ-03); một khoản thu khác 2 triệu | Lập phiếu thu cả hai khoản trên 1113 Tiền mặt - Quỹ phạt | Cả hai ghi nhận thành công — giới hạn nguồn thu, mục đích chi của quỹ phạt bỏ từ 02/11/2026 (PL-C Q-166) |
| UAT-ACC07-13 | Có 1121A01 TCB và 1121A02 VCB, cùng có báo có, báo nợ | Mở sổ tiền gửi 1121A02, lọc TK hạch toán 131 | Chỉ có dòng của 1121A02 đối ứng 131 |
| UAT-ACC07-14 | Lập phiếu chi không chọn **Chứng từ nguồn** | Ghi sổ | Chặn "Phiếu chi phải lập từ đề nghị thanh toán đã duyệt." |
| UAT-ACC07-15 | Đề nghị được duyệt 1,84 tỷ | Lập phiếu chi 1,9 tỷ | Chặn "Đề nghị {mã} được duyệt {giá trị}. Chi vượt phải trình duyệt lại." |
| UAT-ACC07-16 | Cần chuyển 500 triệu từ 1111 Tiền mặt - VP sang 1112 Tiền mặt - Nhà máy | Thực hiện chuyển | Phải lập chứng từ điều chuyển nội bộ; số dư hai quỹ đổi qua chứng từ, không sửa trực tiếp |
| UAT-ACC07-17 | Chi tiền mặt cho một khoản chi phí chung | Lập phiếu chi | Bắt buộc chọn **Dự án = CHUNG** một cách chủ động; không có chứng từ tiền nào thiếu mã dự án |
| UAT-ACC07-18 | Tài khoản 1121A01 dư 300 triệu | Ghi báo nợ 500 triệu trên 1121A01 | Bị chặn như quỹ tiền mặt — hạn mức thấu chi bỏ từ 02/11/2026, tài khoản ngân hàng không được âm (PL-C Q-166) |
| UAT-ACC07-19 | Phiếu số dư ban đầu Nợ 1112 50 triệu; phiếu kế toán (ACC-08) ngày 05/10/2026 Nợ 642 / Có 1112 2 triệu; phiếu thu 10 triệu trên 1112 ngày 06/10/2026, hủy ngày 08/10/2026 | Mở sổ quỹ 1112, kỳ 01/10–31/10/2026 | Sổ mở với 50 triệu từ phiếu số dư ban đầu; có dòng phiếu kế toán chi 2 triệu; phiếu thu hiện dòng gốc 06/10 và dòng đảo 08/10; số dư cuối 48 triệu, khớp số dư 1112 trên bảng cân đối RPT-01 (PL-C Q-151) |
| UAT-ACC07-20 | Tài khoản 1113 dư 1 triệu | Ghi sổ phiếu kế toán Nợ 642 / Có 1113 3 triệu ở ACC-08; rồi lập phiếu chi TM 500.000 trên 1113 | Phiếu kế toán ghi sổ được, sổ 1113 hiện dòng đó và âm 2 triệu; phiếu chi bị chặn "Quỹ {tên} còn {số dư}. Không chi vượt số dư tiền mặt." |
| UAT-ACC07-21 | Kế toán trưởng; chưa có tài khoản 1121A03 | Ở ACC-10 thêm tài khoản con 1121A03 "TGNH Công ty - BIDV"; mở Thu chi | 1121A03 hiện trên dải chọn quỹ với số dư 0 và trong ô tài khoản quỹ của Báo có / Báo nợ, không có trong ô của Phiếu thu / chi TM |

## Ghi chú bao phủ

- BR-01…BR-08 đều có kịch bản nhóm B; "mỗi tài khoản quỹ một sổ" kiểm ở UAT-ACC07-1, -16, -21; "quỹ phạt có nguồn riêng" (BR-03) và hạn mức thấu chi bỏ từ 02/11/2026 — UAT-ACC07-12, -18 kiểm rằng không còn chặn / không còn cho âm; sổ quỹ đọc từ sổ cái kiểm ở UAT-ACC07-19, -20.
- Dòng bảng *Kiểm tra dữ liệu & thông điệp* kiểm ở UAT-ACC07-6/-8/-20 (chi vượt số dư), -14, -15, -11; dòng "thu vào quỹ phạt sai nguồn" đã bỏ 02/11/2026.
- Liên màn hình: **ACC-05** (đề nghị thanh toán), **ORD-01** (hồ sơ chờ thanh toán), **PRJ-03** (khấu trừ phạt), **ACC-06** (chi phí bảo trì), **RPT-04** (dòng tiền), **ACC-10** (tài khoản quỹ 111 / 112), **ACC-08** · **RPT-01** (phiếu kế toán, số dư ban đầu trên sổ quỹ).
