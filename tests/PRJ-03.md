# PRJ-03 · Giao khoán nhân công — Kịch bản kiểm thử

> Nguồn: `spec/PRJ-03.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.
>
> Luồng trình dài nhất của đặc tả — 5 chặng: Ban dự án lập → TBP TQT → P. Đấu thầu → P. Kế toán → Ban lãnh đạo. Có cửa chặn quá hạn riêng trước cả chặng 1.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-PRJ03-1 | `GKNC-014-202607` hạn nộp 31/07, hôm nay 04/08 | Mở hồ sơ, xem khối "Giải trình quá hạn" | Nút "Trình duyệt" khóa; hiện thẻ "Nộp muộn {n} ngày" và ô giải trình quá hạn bắt buộc | CN-1 · BR-01 |
| UAT-PRJ03-2 | Hồ sơ đang ở chặng TBP TQT | Xem dải bước 5 chặng | Hiện đúng thứ tự Dự án lập → TBP TQT → P. Đấu thầu → P. Kế toán → Lãnh đạo, tô đậm chặng hiện tại kèm tiêu chí kiểm riêng của chặng đó | CN-2 · BR-02 |
| UAT-PRJ03-3 | Đang lập hồ sơ kỳ tháng 7 cho tổ TD-004 | Mở bảng khối lượng nghiệm thu | Nạp sẵn khối lượng từ PRJ-02 đợt 4, cột "Nguồn" ghi "PRJ-02 đợt 4" | TS · `work_lines[]` · CN-3 |
| UAT-PRJ03-4 | Sửa tay một dòng khối lượng đã nạp tự động | Lưu | Dòng đổi màu / gắn cờ ngay tại chỗ, hiện nổi bật để chặng 1 (TBP TQT) kiểm | CN-3 · BR-03 |
| UAT-PRJ03-5 | Đầu việc "Gia công đá cong bo góc" chưa có trong bảng giá khoán hiệu lực | Thêm dòng khối lượng cho đầu việc đó | Bị chặn kèm liên kết "Lập phụ lục bảng giá" ở PRJ-01 | VAL-2 · BR-04 |
| UAT-PRJ03-6 | Hồ sơ có dòng công nhật nhưng chưa đính bảng công nhật | Bấm "Lưu" ở bước lập | Bị chặn ngay tại bước lập: "Đính kèm bảng công nhật có xác nhận của tổ đội, kỹ thuật và chỉ huy trưởng." — không đợi tới chặng kế toán mới phát hiện | VAL-4 · BR-07 |
| UAT-PRJ03-7 | Tổ đội còn dư tạm ứng 300 triệu, VTTB 23,4 triệu | Mở bảng khấu trừ | Hai khoản tự điền từ công nợ (ACC-05) và phiếu xuất kho dự án (PRJ-04), không nhập tay | TS · `advances[]`/`materials_issued[]` · CN-4 |
| UAT-PRJ03-8 | Hồ sơ đã được lãnh đạo duyệt | Bấm "Xuất bộ hồ sơ" | Gộp thành một tệp nén theo cấu trúc chuẩn: bảng khối lượng, bảng đơn giá đã duyệt, bảng công nhật có xác nhận, chứng từ phạt/tiện ích/VTTB | CN-6 · KQ-7 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-PRJ03-9 | Hạn nộp 31/07, hôm nay 04/08, giải trình quá hạn chưa nộp | Bấm "Trình duyệt" | Bị chặn; hiện ô giải trình quá hạn và luồng duyệt riêng | BR-01 · VAL-1 · KQ-1 · kịch bản gốc UAT-PRJ03-1 |
| UAT-PRJ03-10 | Tiếp UAT-PRJ03-9, giải trình quá hạn được duyệt riêng | Trình lại | Hồ sơ vào chặng 1 — TBP TQT | BR-01 · kịch bản gốc UAT-PRJ03-2 |
| UAT-PRJ03-11 | Sản lượng tổ TD-004 đợt 4 là 1.680 m² | Lập hồ sơ kỳ tháng 7 | Bảng khối lượng nạp sẵn 1.680, nguồn ghi "PRJ-02 đợt 4" | BR-03 · KQ-2 · kịch bản gốc UAT-PRJ03-3 |
| UAT-PRJ03-12 | Sửa tay khối lượng lên 1.800 (vượt sản lượng đã chốt 1.680) | Trình duyệt | Dòng gắn cờ; chặng 1 thấy cảnh báo: "Khối lượng 1.800 vượt sản lượng đã chốt của tổ TD-004 trong kỳ (1.680)." | VAL-3 · BR-03 · KQ-2 · kịch bản gốc UAT-PRJ03-4 |
| UAT-PRJ03-13 | Hồ sơ đang ở chặng 2 — P. Đấu thầu | TBP TQT (đã duyệt chặng 1) thử duyệt lại nội dung chặng 2 | Bị chặn — chỉ vai trò của chặng hiện tại mới thao tác được; tiêu chí hiển thị đúng của chặng 2 (đơn giá vs bảng giá khoán), không lặp lại tiêu chí chặng 1 | BR-02 |
| UAT-PRJ03-14 | Tổng giá trị khoán 21.840.000, tổng khấu trừ (phạt + tiện ích + VTTB + tạm ứng) = 30.000.000 | Tính giá trị thực trả | `net_amount = 0`; hệ thống hiện rõ số dư 8.160.000 chuyển sang kỳ sau, không cho ra số âm | VAL-5 · BR-06 · KQ-4 |
| UAT-PRJ03-15 | Hồ sơ ở chặng 3 — P. Kế toán, thiếu biên bản phạt | Kế toán bấm "Trả về" kèm lý do | Hồ sơ về người lập, giữ nguyên mã, ghi rõ chặng 3 trả và lý do; trình lại đi từ chặng 1 | BR-08 · KQ-5 · kịch bản gốc UAT-PRJ03-6 |
| UAT-PRJ03-16 | Hồ sơ được lãnh đạo duyệt (chặng 4) | Mở màn hình đề nghị thanh toán | Đã có đề nghị thanh toán ở ACC-05 với đủ đối tượng, giá trị thực trả và dự án, không nhập lại | BR-09 · KQ-6 · kịch bản gốc UAT-PRJ03-7 |
| UAT-PRJ03-17 | Đã duyệt hồ sơ 3 tổ đội trong quý | Mở báo cáo phân loại nhân công (RPT-02) | Chia đúng ba nhóm: sản lượng / công nhật sản lượng / công nhật khác | KQ-8 · kịch bản gốc UAT-PRJ03-8 |
| UAT-PRJ03-18 | Có khoản phạt trong bảng khấu trừ, chưa đính biên bản | Trình duyệt hồ sơ tới chặng 3 | Bị chặn: "Khoản phạt cần biên bản kèm theo để tổ đội đối chiếu." | VAL-6 · BR-05 |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-1, UAT-9, UAT-10 · BR-02 → UAT-2, UAT-13 · BR-03 → UAT-4, UAT-11, UAT-12 · BR-04 → UAT-5 · BR-05 → UAT-18 · BR-06 → UAT-14 · BR-07 → UAT-6 · BR-08 → UAT-15 · BR-09 → UAT-16. Đủ 9/9.
- **Kết quả mong đợi**: KQ-1 → UAT-9 · KQ-2 → UAT-11, UAT-12 · KQ-3 → UAT-5 · KQ-4 → UAT-7, UAT-14 · KQ-5 → UAT-15 · KQ-6 → UAT-16 · KQ-7 → UAT-8 · KQ-8 → UAT-17. Đủ 8/8.
- Tám kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1 → UAT-9 · -2 → UAT-10 · -3 → UAT-11 · -4 → UAT-12 · -6 → UAT-15 · -7 → UAT-16 · -8 → UAT-17. (Kịch bản gốc -5 "Mở bảng khấu trừ" trùng nội dung với `UAT-PRJ03-7` của bộ này.)
- Bảng "Nội dung kiểm của từng chặng" liệt kê chặng 3 (P. Kế toán) kiểm cả **thuế và bảo hiểm**, nhưng mục *Tham số* không có trường nào cho thuế/bảo hiểm trong bảng khấu trừ (chỉ có 4 nhóm: phạt, tiện ích, VTTB, tạm ứng) — khoảng trống giữa hai mục của chính đặc tả, ghi ở `PL-C`.
- Đặc tả không nói rõ **hạn nộp mặc định** (`deadline`) được cấu hình ở đâu ngoài câu "Cấu hình theo dự án" — không rõ ai có quyền đổi hạn sau khi dự án đã khởi tạo.
- Liên màn hình: **PRJ-01** (bảng giá khoán, chặng 2 đối chiếu), **PRJ-02** (sản lượng đã chốt, chặng 1 đối chiếu), **ORD-09** (đối chiếu khối lượng thu của CĐT), **PRJ-04** (VTTB đã cấp, khấu trừ), **ACC-05** (đề nghị thanh toán sinh sau khi duyệt), **RPT-02** (báo cáo phân loại nhân công).
