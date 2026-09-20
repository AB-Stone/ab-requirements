# ACC-09 · Giá thành & lãi lỗ — Kịch bản kiểm thử

> Nguồn: `spec/ACC-09.html`. Bao phủ 3 chu kỳ trên một bộ dữ liệu, tập hợp chi phí theo khoản mục (khoan xuống chứng từ), kết chuyển theo sản lượng qua TK 154, chi phí chung phân bổ không vào giá thành, chốt kỳ theo thứ tự, mở lại kỳ đảo bút toán, đối chiếu tổng với sổ cái, giá thành nhà máy ghi ngược giá nhập.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC09-1 | Menu **Tài chính → Giá thành & lãi lỗ**, đã có đủ chứng từ quý 3 | Chọn **Kỳ = "Quý"**, rồi đổi sang "Tháng" / "6 tháng" | Bảng chạy trên cùng tập dữ liệu gốc, không cần nạp lại số liệu |
| UAT-ACC09-2 | Bảng **Tập hợp chi phí theo khoản mục** cho `DA-2026-014` | Xem cột **Nguồn** | Mỗi KMCP ghi rõ nguồn: PRJ-04 xuất kho / PRJ-03 giao khoán / ACC-06 / ACC-08 |
| UAT-ACC09-3 | Bảng tập hợp chi phí đang hiển thị | Bấm vào ô **KM-101 · 8,96 tỷ** | Mở danh sách phiếu xuất kho tạo nên con số đó (khoan xuống chứng từ gốc) |
| UAT-ACC09-4 | Khối **Kết chuyển theo sản lượng** | Xem các dòng | Dở dang đầu kỳ / phát sinh trong kỳ / % khối lượng hoàn thành / kết chuyển giá vốn / dở dang cuối kỳ là ô chỉ đọc, hệ thống tính |
| UAT-ACC09-5 | Bảng **Lãi lỗ dự án — đối chiếu với dự thầu** | Xem cấu trúc bảng | Có 2 mức: **Lợi nhuận gộp** (sau giá vốn) và **Lợi nhuận thuần** (sau chi phí chung phân bổ); cột "Chênh" so **biên thuần** với biên dự thầu |
| UAT-ACC09-6 | Kỳ quý 3 còn mở | Bấm **Chạy tính giá thành** hai lần | Lần sau ghi đè kết quả nháp lần trước, không tạo bản trùng |
| UAT-ACC09-7 | Kỳ quý 2 vẫn đang mở | Bấm **Chốt kỳ** cho quý 3 | Thông điệp "Kỳ {kỳ trước} vẫn đang mở. Chốt kỳ theo thứ tự…"; nút không thực thi |
| UAT-ACC09-8 | Dự án có chi phí phát sinh nhưng doanh thu = 0 | Mở bảng lãi lỗ | Dòng hiển thị cảnh báo "chưa có đơn hàng áp giá" thay vì số lỗ đỏ |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC09-9 | Quý 3 đã có đủ chứng từ | Chạy tính giá thành theo quý | Bảng tập hợp chi phí hiện 4 khoản mục với tỷ trọng |
| UAT-ACC09-10 | Tiếp UAT-ACC09-9 | Bấm vào ô `KM-101` 8,96 tỷ | Mở danh sách phiếu xuất kho tạo nên con số này — không có số nào không truy ngược được |
| UAT-ACC09-11 | Sản lượng đã chốt 62% (PRJ-02) | Xem khối kết chuyển | Kết chuyển giá vốn 14,18 tỷ; dở dang cuối kỳ 3,3 tỷ; phần chưa hoàn thành tự thành dở dang, chuyển kỳ sau |
| UAT-ACC09-12 | Đã chạy quý 3 | Chạy lại theo tháng 9 | Ra kết quả tháng 9 trên cùng dữ liệu, không xung đột với kết quả quý |
| UAT-ACC09-13 | Chưa phân bổ chi phí chung kỳ 9 (ACC-08) | Chạy tính giá thành | Cảnh báo "Còn {giá trị} chi phí chung chưa phân bổ. Giá thành sẽ thiếu phần gián tiếp."; vẫn cho chạy để xem thử |
| UAT-ACC09-14 | Quý 3 đã chốt | Ghi phiếu nhập ngày 20/09 | Hệ thống chuyển chứng từ sang kỳ hiện tại kèm thông báo |
| UAT-ACC09-15 | Nhà máy sản xuất và nhập kho thành phẩm | Chạy giá thành nhà máy | Giá nhập kho thành phẩm cập nhật ngược; ảnh hưởng giá xuất kho kỳ sau (vòng khép kín) |
| UAT-ACC09-16 | Kỳ quý 3 đã chốt | Mở báo cáo đối chiếu tổng | Tổng chi phí trực tiếp + phân bổ của mọi dự án khớp sổ cái, chênh lệch bằng 0 |
| UAT-ACC09-17 | Chốt kỳ quý 3 | Xem bút toán kết chuyển sinh ra | Nợ 154 / Có 621, 622, 627 tập hợp chi phí sản xuất; rồi Nợ 632 / Có 154 phần hoàn thành; số dư 154 của `DA-2026-014` = dở dang cuối kỳ 3,3 tỷ, đọc được trên sổ |
| UAT-ACC09-18 | Sản lượng kỳ bị điều chỉnh giảm, kết chuyển ra số âm | Chốt kỳ | Hệ thống **đảo chiều** bút toán, không ghi số âm |
| UAT-ACC09-19 | Chi phí chung phân bổ (TK 642) về `DA-2026-014` | Xem đường đi của khoản này | Không đi qua 154, không thành dở dang; đi thẳng vào lợi nhuận thuần của kỳ (PL-C Q-23 phương án a) |
| UAT-ACC09-20 | Quý 3 đã chốt, cần sửa số liệu | Mở lại kỳ (quyền Kế toán trưởng) | Phiếu đảo bút toán kết chuyển mang ngày **trong quý 3**, không phải ngày hôm nay; thao tác ghi nhật ký kèm lý do |
| UAT-ACC09-21 | Tiếp UAT-ACC09-20, đã cập nhật số liệu mới | Đóng lại quý 3 | Kết chuyển lại từ đầu theo số liệu mới |
| UAT-ACC09-22 | Báo cáo đối chiếu tổng phát hiện lệch | Xem chi tiết đối chiếu | Chỉ ra khoản mục gây lệch: {danh sách} |
| UAT-ACC09-23 | Dự án chưa chốt sản lượng kỳ này | Chạy kết chuyển chi phí | "Dự án {mã} chưa chốt sản lượng kỳ này. Không có cơ sở kết chuyển chi phí." |

## Ghi chú bao phủ

- BR-01…BR-13 đều có kịch bản nhóm B; bút toán 154 và ranh giới chi phí chung (Q-23) kiểm ở UAT-ACC09-17, -19.
- 7 dòng bảng *Kiểm tra dữ liệu & thông điệp* kiểm ở UAT-ACC09-13, -23, -8, -14, -22, -7 (chốt kỳ vượt thứ tự), và -21 (kỳ đã kết chuyển).
- Liên màn hình đầu vào: **PRJ-02** (sản lượng), **PRJ-03** (nhân công), **PRJ-04** (vật tư), **ACC-06** (khấu hao), **ACC-08** (phân bổ chung), **ACC-03** (mua không qua kho), **BID-06 / ORD-09** (doanh thu). Đầu ra: **RPT-03** (báo cáo lãi lỗ), **BID-03** (đối chiếu biên dự thầu).
