# ACC-08 · Phiếu kế toán & phân bổ — Kịch bản kiểm thử

> Nguồn: `spec/ACC-08.html`. Bao phủ lưới nhập phiếu kế toán (4 cột quản trị, cân đối nợ–có), bảng phân bổ chi phí chung sinh bản nháp, đổi tiêu chí tính lại, một kỳ một lần phân bổ mỗi nhóm, sau phân bổ khối CHUNG về 0.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC08-1 | Vai trò Kế toán tổng hợp | Menu **Tài chính → Phiếu kế toán & phân bổ → + Phiếu kế toán** | Lưới nhập nhiều dòng; mỗi dòng có đủ 4 cột: Mã dự án, KMCP, Mã thống kê, CP hợp lý |
| UAT-ACC08-2 | Đã nhập các dòng bút toán lệch nợ–có | Xem dòng tổng dưới lưới | Hiển thị "Tổng nợ … ≠ tổng có …" kèm chênh lệch; nút **Lưu** mờ |
| UAT-ACC08-3 | Đang ở màn hình phiếu kế toán | Chuyển sang tab **Phân bổ chi phí chung** | Tab tách hẳn khỏi lưới nhập liệu thường; chọn được kỳ, nhóm chi phí, tiêu chí, cấp phân bổ |
| UAT-ACC08-4 | Tab phân bổ, chọn nhóm chi phí kỳ tháng 7 | Chọn **Tiêu chí = "Theo doanh thu ghi nhận"** | Bảng nháp hiện tỷ trọng và số tiền phân bổ cho từng dự án; "Tổng cần phân bổ" khớp tổng nhóm |
| UAT-ACC08-5 | Tiếp UAT-ACC08-4 | Bấm **Đổi tiêu chí, tính lại** sang "chi phí trực tiếp" | Bảng cập nhật ngay tại chỗ, không phải lập phiếu mới |
| UAT-ACC08-6 | Tab phân bổ | Chọn **Tiêu chí = "Tỷ lệ ấn định"**, nhập tổng ≠ 100% | Bảng tỷ lệ nhập tay xuất hiện; thông điệp "Tổng tỷ lệ đang là {x}%. Điều chỉnh cho đủ 100%." |
| UAT-ACC08-7 | Bảng phân bổ nháp đã đúng | Bấm **Trình KTT duyệt & ghi sổ**, KTT duyệt | Bảng nháp sinh bút toán và khóa lại, trạng thái "đã ghi sổ" |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC08-8 | Nhập 3 dòng lương, tổng nợ 1,308 tỷ | Nhập tổng có 1,3 tỷ và **Lưu** | Chặn lưu, hiện chênh lệch 8 triệu |
| UAT-ACC08-9 | Chi phí chung kỳ 7 là 574,6 triệu | Lập bảng phân bổ theo doanh thu | Bảng nháp hiện tỷ trọng và số tiền về từng dự án |
| UAT-ACC08-10 | Tiếp UAT-ACC08-9 | Đổi tiêu chí sang "chi phí trực tiếp" | Bảng tính lại ngay, không phải lập phiếu mới |
| UAT-ACC08-11 | Bảng nháp đã đúng | Kế toán trưởng duyệt | Sinh bút toán phân bổ; bảng khóa lại |
| UAT-ACC08-12 | Đã phân bổ nhóm `KM-401` kỳ 7 | Lập bảng phân bổ mới cùng nhóm, cùng kỳ | Bị chặn kèm mã bảng đã dùng |
| UAT-ACC08-13 | Muốn làm lại phân bổ kỳ 7 | Hủy bảng phân bổ cũ | Thao tác hủy ghi nhật ký; sau đó mới lập lại được bảng mới |
| UAT-ACC08-14 | Đã phân bổ xong toàn bộ kỳ 7 | Mở báo cáo đối chiếu chi phí chung | Số dư CHUNG của kỳ bằng 0 |
| UAT-ACC08-15 | Còn một nhóm chi phí chưa phân bổ | Mở báo cáo đối chiếu | "Còn {giá trị} chi phí chung chưa phân bổ ở các khoản mục: {danh sách}." |
| UAT-ACC08-16 | Có chi phí đánh dấu không hợp lý 104 triệu | Phân bổ và mở báo cáo | Chi phí vẫn về dự án nhưng được tách riêng trong báo cáo chi phí không hợp lý |
| UAT-ACC08-17 | Chọn tiêu chí mà mọi dự án đều bằng 0 theo tiêu chí đó | Lập bảng phân bổ | "Không dự án nào có {tiêu chí} trong kỳ này. Chọn tiêu chí khác hoặc dùng tỷ lệ ấn định." |
| UAT-ACC08-18 | Có dữ liệu sản lượng theo hạng mục đầy đủ | Chọn **Phân bổ tới cấp = "hạng mục"** | Sổ chi phí công trình chi tiết đến từng hạng mục |

## Ghi chú bao phủ

- BR-01…BR-08 đều có kịch bản nhóm B; "sau phân bổ khối CHUNG về 0" kiểm ở UAT-ACC08-14, -15.
- 5 dòng bảng *Kiểm tra dữ liệu & thông điệp* kiểm ở UAT-ACC08-8/-2, -12, -6, -17, -15.
- Liên màn hình: **PF-01** (khoản mục & mã thống kê), **PF-03** (KTT duyệt bảng phân bổ), **ACC-06** (khấu hao kho tổng vào khối chung), **ACC-09** (kết chuyển lãi lỗ), **RPT-01 / RPT-03**.
