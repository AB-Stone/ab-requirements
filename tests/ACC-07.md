# ACC-07 · Thu chi — quỹ & ngân hàng — Kịch bản kiểm thử

> Nguồn: `spec/ACC-07.html`. Bao phủ 3 quỹ tiền mặt độc lập, không chi vượt số dư quỹ, quỹ phạt có nguồn riêng, sổ tiền gửi 3 chiều, phiếu chi phải có đề nghị, đối chiếu báo có, nhập sao kê hàng loạt.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC07-1 | Menu **Tài chính → Sổ quỹ & ngân hàng** | Chọn lần lượt Quỹ công ty / Quỹ nhà máy / Quỹ phạt / Vietcombank / BIDV | Mỗi nơi có sổ riêng; KPI số dư đầu kỳ / thu / chi / cuối kỳ và bảng chứng từ đổi theo, tính độc lập |
| UAT-ACC07-2 | Mở sổ tiền gửi ngân hàng | Dùng bộ lọc | Chọn được đồng thời 3 chiều: ngân hàng, tài khoản hạch toán, số tài khoản ngân hàng |
| UAT-ACC07-3 | Bấm **+ Phiếu chi** | Xem form | Trường **Chứng từ nguồn** bắt buộc; chọn đề nghị thanh toán đã duyệt thì số tiền mặc định bằng giá trị đề nghị, không cho nhập vượt |
| UAT-ACC07-4 | Có báo có ngân hàng chưa đối chiếu | Mở tab **Báo có chờ đối chiếu** | Mỗi dòng hiển thị "Hồ sơ khớp gợi ý" kèm nút "Xác nhận khớp" / "Xem" |
| UAT-ACC07-5 | Có tệp sao kê ngân hàng | Bấm **Nhập sao kê**, tải tệp | Bảng kết quả: phần khớp tự động được đánh dấu; phần còn lại chuyển tab "Chờ đối chiếu" |
| UAT-ACC07-6 | Quỹ công ty dư 1,74 tỷ | Lập phiếu chi tiền mặt 2 tỷ | Thông điệp "Quỹ {tên} còn {số dư}. Không chi vượt số dư tiền mặt."; nút ghi sổ không thực thi |
| UAT-ACC07-7 | Đang xem sổ quỹ tiền mặt | Bấm **Xuất Excel** | Tệp theo kỳ, đủ chiều phân tích: đối tượng, dự án, tài khoản hạch toán |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC07-8 | Quỹ công ty dư 1,74 tỷ | Lập phiếu chi 2 tỷ tiền mặt | Bị chặn kèm số dư hiện tại |
| UAT-ACC07-9 | Đề nghị `DNTT-2026-0271` được duyệt 1,84 tỷ | Lập ủy nhiệm chi 1,84 tỷ | Ghi sổ ngân hàng, giảm công nợ, cập nhật sổ chi tiết đối tác |
| UAT-ACC07-10 | Báo có 3,2 tỷ nội dung "COTECCONS TT DOT 4 HD 2026-008" | Mở tab đối chiếu | Gợi ý đúng `HSTU-2026-011`; xác nhận thì hồ sơ tự đóng, công nợ phải thu giảm — không nhập tay hai lần |
| UAT-ACC07-11 | Tệp sao kê 60 giao dịch | Tải lên | Khớp tự động phần rõ ràng; phần còn lại vào tab chờ đối chiếu, không tự đoán bừa |
| UAT-ACC07-12 | Khấu trừ phạt 8 triệu từ hồ sơ giao khoán (PRJ-03) | Lập phiếu thu vào quỹ phạt | Ghi nhận thành công; thử thu từ nguồn khác vào quỹ phạt thì bị chặn |
| UAT-ACC07-13 | Có 2 ngân hàng, 3 tài khoản | Mở sổ tiền gửi và lọc | Lọc được đồng thời theo ngân hàng, TK hạch toán và số tài khoản ngân hàng |
| UAT-ACC07-14 | Lập phiếu chi không chọn **Chứng từ nguồn** | Ghi sổ | Chặn "Phiếu chi phải lập từ đề nghị thanh toán đã duyệt." |
| UAT-ACC07-15 | Đề nghị được duyệt 1,84 tỷ | Lập phiếu chi 1,9 tỷ | Chặn "Đề nghị {mã} được duyệt {giá trị}. Chi vượt phải trình duyệt lại." |
| UAT-ACC07-16 | Cần chuyển 500 triệu từ quỹ công ty sang quỹ nhà máy | Thực hiện chuyển | Phải lập chứng từ điều chuyển nội bộ; số dư hai quỹ đổi qua chứng từ, không sửa trực tiếp |
| UAT-ACC07-17 | Chi tiền mặt cho một khoản chi phí chung | Lập phiếu chi | Bắt buộc chọn **Dự án = CHUNG** một cách chủ động; không có chứng từ tiền nào thiếu mã dự án |
| UAT-ACC07-18 | Tài khoản ngân hàng đã khai hạn mức thấu chi | Ghi phiếu chi vượt số dư nhưng trong hạn mức thấu chi | Cho số dư âm tạm thời; cùng tình huống với quỹ tiền mặt thì bị chặn |

## Ghi chú bao phủ

- BR-01…BR-08 đều có kịch bản nhóm B; "ba quỹ ba sổ độc lập" kiểm ở UAT-ACC07-1, -16.
- 5 dòng bảng *Kiểm tra dữ liệu & thông điệp* kiểm ở UAT-ACC07-6/-8, -14, -15, -12, -11.
- Liên màn hình: **ACC-05** (đề nghị thanh toán), **ORD-01** (hồ sơ chờ thanh toán), **PRJ-03** (khấu trừ phạt), **ACC-06** (chi phí bảo trì), **RPT-04** (dòng tiền).
