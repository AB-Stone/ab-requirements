# ACC-06 · TSCĐ & CCDC — Kịch bản kiểm thử

> Nguồn: `spec/ACC-06.html`. Bao phủ vòng đời tài sản, mua mới vào kho tổng, tài sản cũ không khấu hao, điều chuyển cần xác nhận nơi nhận, khấu hao phân bổ theo số ngày, ghi giảm sinh 3 bút toán, tra cứu QR.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC06-1 | Vai trò Kế toán tài sản | Menu **Tài sản → + Ghi tăng tài sản** | Form mở; trường **Vị trí hiện tại** mặc định "Kho tổng công ty", không phải chọn tay |
| UAT-ACC06-2 | Form ghi tăng tài sản | Bật cờ **Tài sản cũ** | Toàn bộ nhóm trường khấu hao (phương pháp, thời gian, KH kỳ này) ẩn khỏi form |
| UAT-ACC06-3 | Hồ sơ tài sản `TS-0012` | Mở phần **Lịch sử di chuyển & phân bổ khấu hao** | Dòng thời gian hiển thị từng chặng vị trí kèm số ngày, KH phân bổ và người xác nhận nhận |
| UAT-ACC06-4 | Hồ sơ tài sản đang ở dự án | Bấm **Điều chuyển**, chọn nơi đến, trình duyệt | Trạng thái "chờ {GĐ dự án nơi nhận} xác nhận"; mốc **Số ngày tại vị trí** của nơi mới vẫn là 0 |
| UAT-ACC06-5 | Hồ sơ tài sản có khấu hao | Xem khối khấu hao | Nguyên giá / Đã khấu hao % / Giá trị còn lại / KH kỳ này là ô chỉ đọc, do hệ thống tính theo kỳ |
| UAT-ACC06-6 | Hồ sơ tài sản có mã QR định danh | Quét QR trên điện thoại (MOB-01) | Màn hình tra cứu hiện tên tài sản, vị trí hiện tại và dự án đang phục vụ |
| UAT-ACC06-7 | Tài sản đang ở dự án đang thi công | Bấm **Ghi giảm** | Thông điệp "Tài sản đang phục vụ {dự án}. Điều chuyển về kho tổng trước khi ghi giảm." |
| UAT-ACC06-8 | Form khai báo khấu hao | Nhập **thời gian sử dụng** ngoài khung nhóm tài sản | Cảnh báo "Thời gian sử dụng {n} năm nằm ngoài khung {a}–{b} năm của nhóm tài sản này." |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC06-9 | Mua máy cắt mới 2,85 tỷ | Ghi tăng tài sản | Vị trí mặc định là kho tổng công ty |
| UAT-ACC06-10 | Tài sản mua lại đã qua sử dụng | Bật cờ "tài sản cũ" | Nhóm trường khấu hao ẩn; không sinh bút toán khấu hao; vẫn theo dõi hiện vật và vị trí |
| UAT-ACC06-11 | Phiếu điều chuyển tới `DA-2026-014` đã duyệt, chưa để GĐ dự án xác nhận | Chạy khấu hao kỳ | Khấu hao vẫn tính cho vị trí cũ |
| UAT-ACC06-12 | GĐ dự án xác nhận ngày 12/07 | Chạy khấu hao quý 3 | Chia theo số ngày: trước 12/07 cho nơi cũ, từ 12/07 cho `DA-2026-014` |
| UAT-ACC06-13 | Tài sản ở kho tổng 70 ngày trong kỳ | Chạy khấu hao kỳ | Phần 70 ngày ghi vào **chi phí chung** chờ phân bổ ở ACC-08 |
| UAT-ACC06-14 | Thanh lý tài sản; biên bản ghi ngày 15/08, nhập liệu ngày 22/08 | Ghi giảm | Ngưng khấu hao từ **15/08**; sinh đủ 3 bút toán (giảm nguyên giá, giảm hao mòn lũy kế, thu nhập bán thanh lý) trong một giao dịch |
| UAT-ACC06-15 | Tài sản đang ở công trường | Quét QR trên tài sản | Hiện tên, vị trí hiện tại và dự án đang phục vụ trong dưới 2 giây |
| UAT-ACC06-16 | 42 tài sản ở nhiều vị trí | Mở báo cáo tổng hợp CCDC, TSCĐ | Tách nhóm theo dự án / không theo dự án; xếp theo vị trí tồn hiện tại |
| UAT-ACC06-17 | Phiếu điều chuyển quá 7 ngày chưa xác nhận | Xem danh sách điều chuyển | Cảnh báo "Phiếu điều chuyển {mã} chờ {người} xác nhận đã {n} ngày. Tài sản vẫn tính cho {nơi cũ}." |
| UAT-ACC06-18 | Kỳ khấu hao đã chốt | Chạy trích khấu hao cho kỳ đó | Thông điệp "Kỳ {kỳ} đã chốt. Khấu hao sẽ ghi vào kỳ hiện tại." |
| UAT-ACC06-19 | Ghi nhận bảo trì ở tab **Lịch sử bảo trì** | Nhập ngày, nội dung, chi phí | Chi phí bảo trì vào chi phí kỳ (liên kết ACC-07), **không** cộng vào nguyên giá tài sản |

## Ghi chú bao phủ

- BR-01…BR-09 đều có kịch bản nhóm B; "số ngày là hệ số phân bổ" kiểm ở UAT-ACC06-12, -13.
- 5 dòng bảng *Kiểm tra dữ liệu & thông điệp* kiểm ở UAT-ACC06-7, -8, -17, -18 và -2/-10 (tài sản cũ + khấu hao).
- Liên màn hình: **ACC-01** (đề xuất mua TS, CCDC), **ACC-02** (tài sản tại kho tổng), **ACC-03** (phiếu nhập ghi nguyên giá), **ACC-07** (chi phí bảo trì), **ACC-08** (phân bổ khấu hao chung), **RPT-01**, **MOB-01** (quét QR).
