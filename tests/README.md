# Kịch bản kiểm thử — ABSMS Giai đoạn 1

Bộ kịch bản kiểm thử chấp nhận (UAT) cho các phân hệ của ABSMS Giai đoạn 1.
Mỗi màn hình một tệp, bám theo đặc tả cùng mã trong thư mục `spec/`.

## Phạm vi

Mỗi tệp chia làm hai nhóm:

| Nhóm | Nội dung | Trả lời câu hỏi |
|---|---|---|
| **A · Luồng giao diện** | Điều hướng vào màn hình, chuyển tab / bộ lọc, hiện–ẩn trường theo điều kiện, tính lại tại chỗ, ô chỉ đọc, trạng thái nút, thông điệp kiểm tra hiển thị trên màn hình. | Người dùng thao tác trên giao diện thì màn hình phản ứng đúng chưa? |
| **B · Chức năng & nghiệp vụ** | Quy tắc nghiệp vụ (BR), định khoản kế toán, tính toán, chuyển trạng thái chứng từ, ảnh hưởng lan sang màn hình khác, các tình huống biên. | Kết quả ghi sổ và số liệu có đúng đặc tả không? |

## Quy ước mã

- `UAT-<MÃ MÀN HÌNH>-<số>` — đánh số liên tục trong một màn hình; nhóm A đánh trước, nhóm B tiếp nối.
- Tệp của phân hệ BID có thêm cột **Nguồn**: mỗi kịch bản trỏ về đúng một mục của đặc tả — `TS` (Tham số), `CN-n` (Chức năng trên màn hình), `BR-nn` (Quy tắc nghiệp vụ), `VAL-n` (Kiểm tra dữ liệu & thông điệp), `KQ-n` (Kết quả mong đợi). Không có kịch bản nào nằm ngoài đặc tả; khoảng trống của đặc tả được ghi ở mục *Ghi chú bao phủ* thay vì tự đặt ra quy tắc mới.
- Cột **Tiền đề** ghi trạng thái dữ liệu trước khi thao tác; "Tiếp UAT-x" nghĩa là dùng lại kết quả của kịch bản trước.
- Số liệu ví dụ (mã dự án, giá trị, tỷ lệ) lấy từ mockup trong đặc tả tương ứng để tái lập được.

## Danh mục — Phòng Đấu thầu (BID)

| Tệp | Màn hình | Số kịch bản (A + B) |
|---|---|---|
| [BID-01.md](BID-01.md) | Tiếp nhận thư chào thầu | 25 + 64 |
| [BID-02.md](BID-02.md) | Thẩm định BOQ liên phòng ban | 15 + 28 |
| [BID-03.md](BID-03.md) | Áp giá, gửi báo giá & thương thảo | 23 + 46 |
| [BID-04.md](BID-04.md) | Huỷ gói thầu & thông báo các bộ phận | 7 + 11 |
| [BID-05.md](BID-05.md) | Ký hợp đồng & khởi tạo dự án | 14 + 42 |
| [BID-06.md](BID-06.md) | Áp giá doanh thu cho đơn hàng | 12 + 15 |

## Danh mục — Phòng Kế toán (ACC)

| Tệp | Màn hình | Số kịch bản (A + B) |
|---|---|---|
| [ACC-01.md](ACC-01.md) | Đề xuất mua sắm tài sản, CCDC, vật tư & chi phí | 8 + 11 |
| [ACC-02.md](ACC-02.md) | Hệ thống kho | 7 + 11 |
| [ACC-03.md](ACC-03.md) | Mua hàng & nhập kho | 8 + 11 |
| [ACC-04.md](ACC-04.md) | Xuất kho & bán hàng | 10 + 9 |
| [ACC-05.md](ACC-05.md) | Công nợ & thanh toán | 8 + 11 |
| [ACC-06.md](ACC-06.md) | TSCĐ & CCDC | 8 + 11 |
| [ACC-07.md](ACC-07.md) | Thu chi — quỹ & ngân hàng | 7 + 11 |
| [ACC-08.md](ACC-08.md) | Phiếu kế toán & phân bổ | 7 + 11 |
| [ACC-09.md](ACC-09.md) | Giá thành & lãi lỗ | 8 + 15 |

## Danh mục — Dự án — Thi công (PRJ)

| Tệp | Màn hình | Số kịch bản (A + B) |
|---|---|---|
| [PRJ-01.md](PRJ-01.md) | Giá khoán tổ đội | 20 + 24 |
| [PRJ-02.md](PRJ-02.md) | Sản lượng thi công | 7 + 9 |
| [PRJ-03.md](PRJ-03.md) | Giao khoán nhân công | 8 + 10 |
| [PRJ-04.md](PRJ-04.md) | Xuất nhập kho dự án | 8 + 10 |

## Kết quả chạy

| Tệp | Đợt | Môi trường |
|---|---|---|
| [KQ-UAT-ACC-20260903.md](KQ-UAT-ACC-20260903.md) | Toàn phân hệ ACC, 03/09/2026 | `dev.absms.site` v2.2.0 |
| [KQ-UAT-BID-20260903.md](KQ-UAT-BID-20260903.md) | Toàn phân hệ BID, 03/09/2026 | `dev.absms.site` v2.2.0 |
| [KQ-UAT-PRJ-20260905.md](KQ-UAT-PRJ-20260905.md) | Toàn phân hệ PRJ, 05/09/2026 | `dev.absms.site` |

## Độ phủ mã nguồn

| Tệp | Phạm vi | Ngày |
|---|---|---|
| [DOPHU-BID-20260904.md](DOPHU-BID-20260904.md) | Endpoint API · unit test Go · component `abs-ms` của phân hệ BID | 04/09/2026 |

## Luồng kiểm thử liên phân hệ (chạy end-to-end)

1. **Dòng gói thầu → dự án**: BID-01 tiếp nhận và số hoá BOQ → BID-02 hai phiếu — *Rà soát, kiểm tra Hồ sơ mời thầu* (P. TQT) và *Khảo sát hiện trường* (Ban dự án) — hợp nhất thành BOQ v2 → BID-03 áp giá, gửi báo giá, chốt giá → BID-05 kiểm tra trước ký, ký số, khởi tạo dự án (sinh kho ở ACC-02, WBS cho PRJ-02, đợt thanh toán cho ORD-01, hồ sơ bản vẽ thiết kế và hồ sơ shopdrawing ở ORD-02). Nhánh rẽ: BID-03 trượt thầu → BID-04; gói gộp ở BID-01 thì huỷ gói đích, không huỷ gói nguồn.
2. **Dòng chi phí dự án**: ACC-01 duyệt đề xuất → ACC-03 nhập kho (ghi chi phí trước VAT) → ACC-05 phát sinh phải trả → ACC-07 chi trả (3 sổ) → ACC-09 kết chuyển giá thành.
3. **Dòng vật tư ra công trường**: ACC-03 nhập kho nhà máy → ACC-04 xuất thi công (điều chuyển nội bộ) → PRJ-04 xuất cho tổ đội (ghi chi phí) → ACC-09.
4. **Dòng tài sản & khấu hao**: ACC-06 ghi tăng (kho tổng) → điều chuyển ra dự án → khấu hao theo số ngày → ACC-08 phân bổ phần kho tổng → ACC-09.
5. **Dòng cuối kỳ**: ACC-08 phân bổ chi phí chung (khối CHUNG về 0) → ACC-09 chạy giá thành → chốt kỳ → đối chiếu tổng với sổ cái.
6. **Dòng đối chiếu biên lợi nhuận**: BID-03 chốt giá (lưu biên dự thầu) → PRJ-02 sản lượng → ACC-09 giá thành thực tế → BID-06 duyệt giá doanh thu từng đơn (điểm ghi nhận doanh thu) → RPT-03 đối chiếu biên dự thầu ↔ biên thực tế. Dự án chưa đơn nào áp giá thì RPT-03 cảnh báo thay vì hiện lỗ giả.
