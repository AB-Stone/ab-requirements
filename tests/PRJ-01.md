# PRJ-01 · Giá khoán tổ đội — Kịch bản kiểm thử

> Nguồn: `spec/PRJ-01.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.
>
> Luồng trình ba chặng: Ban dự án lập → P. Đấu thầu thẩm định → Ban lãnh đạo duyệt. Chốt kiểm chính là chặng giữa: đối chiếu giá khoán đề xuất với giá đã dùng để tính giá chào thầu ở BID-03.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-PRJ01-1 | Dự án `DA-2026-014` vừa khởi tạo, có BOQ hợp đồng | Mở màn hình Giá khoán tổ đội | Hiện đúng thứ tự 3 chặng "Ban dự án lập → P. Đấu thầu thẩm định → Ban lãnh đạo duyệt" kèm người phụ trách và thời điểm của chặng đã qua | CN-1 |
| UAT-PRJ01-2 | Đang lập dòng giá khoán | Mở ô chọn đầu việc | Chỉ liệt kê đầu việc thuộc BOQ hợp đồng của dự án, không gõ tự do; cột đơn giá dự thầu nạp sẵn, chỉ đọc | TS · `rates[].wbs_id` · CN-2 · BR-02 · kịch bản gốc UAT-PRJ01-1 |
| UAT-PRJ01-3 | Đơn giá dự thầu HM-02 là 380.000 | Nhập đơn giá khoán 412.000 | Cột chênh lệch tính lại tại chỗ, hiện +8,4% và tô cảnh báo; ô giải trình bật hiện ngay | CN-3 · BR-03 · kịch bản gốc UAT-PRJ01-2 |
| UAT-PRJ01-4 | Có dòng vượt đơn giá dự thầu, ô giải trình đang trống | Bấm "Trình duyệt" | Bị chặn: "Có {n} dòng vượt đơn giá dự thầu. Nhập giải trình trước khi trình duyệt." | VAL-3 · BR-04 · kịch bản gốc UAT-PRJ01-3 |
| UAT-PRJ01-5 | Bảng đang ở chặng P. Đấu thầu, ô ý kiến trống | Bấm chuyển tiếp sang Ban lãnh đạo | Bị chặn: "Nhập ý kiến thẩm định, kể cả khi đồng thuận — lãnh đạo cần căn cứ để quyết định." | VAL-4 · BR-05 · kịch bản gốc UAT-PRJ01-4 |
| UAT-PRJ01-6 | Bảng giá vừa được lãnh đạo duyệt | Mở lại hồ sơ, thử sửa ô đơn giá | Ô đơn giá khoán chuyển chỉ đọc; xuất hiện nút "Lập phụ lục" thay cho nút sửa | KQ-5 · BR-06 · kịch bản gốc UAT-PRJ01-5 |
| UAT-PRJ01-7 | Đặt ngày hiệu lực 01/01, ngày duyệt dự kiến 02/02 | Bấm "Duyệt" | Bị chặn: "Bảng giá khoán phải được duyệt trước khi triển khai. Ngày hiệu lực sớm nhất là {ngày duyệt}." | VAL-1 · BR-01 · kịch bản gốc UAT-PRJ01-7 |
| UAT-PRJ01-8 | Tổ đội TD-099 chưa được gán vào dự án `DA-2026-014` | Chọn TD-099 làm tổ đội nhận đầu việc | Bị chặn: "Tổ TD-099 chưa được gán vào dự án này. Gán tại danh mục tổ đội trước." | VAL-7 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-PRJ01-9 | Bảng `GK-014-01` hoàn tất 3 dòng, không dòng nào vượt dự thầu | Trình đủ 3 chặng: Dự án lập → P. Đấu thầu (ý kiến "Đồng thuận") → Ban lãnh đạo duyệt | Trạng thái chuyển "Có hiệu lực"; đơn giá khóa vĩnh viễn trên mọi dòng; sửa chỉ còn qua nút "Lập phụ lục" | BR-06 · KQ-5 · CN-1 |
| UAT-PRJ01-10 | Dự án `DA-2026-011` chưa có bảng giá khoán hiệu lực (đang ở chặng P. Đấu thầu) | Vào PRJ-03, bấm "Lập hồ sơ giao khoán" cho dự án đó | Bị chặn kèm liên kết trực tiếp tới bảng giá đang chờ duyệt | BR-07 · KQ-1 · kịch bản gốc UAT-PRJ01-6 |
| UAT-PRJ01-11 | Giá khoán HM-02 (519.120.000) vượt doanh thu BOQ của chính HM-02 | Bấm "Trình duyệt" mà chưa xác nhận hạng mục lỗ | Bị chặn: "Giá khoán hạng mục HM-02 vượt doanh thu hợp đồng của chính hạng mục đó. Hạng mục này chắc chắn lỗ. Xác nhận chấp thuận trước khi trình duyệt." | VAL-6 · BR-08 |
| UAT-PRJ01-12 | Tiếp UAT-PRJ01-11 | Nhập xác nhận chấp thuận (`loss_ack`) rồi trình lại | Đi tiếp sang chặng kế; xác nhận ghi rõ người và thời điểm, hiện nguyên văn ở màn hình thẩm định và màn hình duyệt | BR-08 · KQ-9 · kịch bản gốc UAT-PRJ01-8 |
| UAT-PRJ01-13 | Bảng giá đã duyệt có 3 dòng (HM-01, HM-02, HM-04) | Kiểm tra khối "Đối chiếu tổng thể" | Tổng giá khoán đề xuất so được trực tiếp với nhân công theo dự thầu và tỷ trọng/doanh thu HĐ, không cần tính tay | KQ-2 · KQ-8 |
| UAT-PRJ01-14 | Bảng giá `GK-014-01` đã duyệt, có đầu việc "Ốp lát mặt đứng" | Lập giá vốn nhân công cho gói thầu mới cùng loại đầu việc, cùng vùng miền | Đơn giá đã duyệt được gợi ý làm giá vốn mặc định, ưu tiên dự án cùng vùng miền và cùng loại hình công trình | BR-09 · KQ-7 |
| UAT-PRJ01-15 | Bảng giá gốc `GK-014-01` đã duyệt, phát sinh đầu việc HM-06 ngoài BOQ hợp đồng | Thêm dòng giá khoán cho HM-06 trực tiếp vào bảng gốc | Bị chặn: "Đầu việc này không có trong BOQ hợp đồng. Lập phụ lục bảng giá cho đầu việc phát sinh." — phải lập phụ lục `GK-014-01-PL1` | VAL-2 · BR-02 |
| UAT-PRJ01-16 | Phụ lục `GK-014-01-PL1` vừa lập cho HM-06 | So sánh với bản gốc `GK-014-01` | Bản gốc giữ nguyên không đổi; phụ lục là hồ sơ riêng, đi qua đủ 3 chặng phê duyệt như bảng gốc | BR-06 · CN-5 |
| UAT-PRJ01-17 | Người dùng không thuộc luồng trình của dự án (không phải Ban dự án / P. Đấu thầu / Ban lãnh đạo liên quan) | Mở hồ sơ bảng giá khoán | Xem được nhưng không thao tác/duyệt được — quyền theo luồng trình | Quyền truy cập (mục *Quyền truy cập* của trang · PL-B) |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-7 · BR-02 → UAT-2, UAT-15 · BR-03 → UAT-3 · BR-04 → UAT-4 · BR-05 → UAT-5 · BR-06 → UAT-6, UAT-9, UAT-16 · BR-07 → UAT-10 · BR-08 → UAT-11, UAT-12 · BR-09 → UAT-14. Đủ 9/9.
- **Kết quả mong đợi**: KQ-1 → UAT-10 · KQ-2 → UAT-13 · KQ-3 → UAT-4 · KQ-4 → UAT-5 · KQ-5 → UAT-6, UAT-9, UAT-16 · KQ-6 → UAT-7 · KQ-7 → UAT-14 · KQ-8 → UAT-13 · KQ-9 → UAT-12. Đủ 9/9.
- Tám kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1 → UAT-2 · -2 → UAT-3 · -3 → UAT-4 · -4 → UAT-5 · -5 → UAT-6 · -6 → UAT-10 · -7 → UAT-7 · -8 → UAT-11/-12.
- Đặc tả không nói rõ **thuật toán gợi ý** của BR-09 ("ưu tiên dự án cùng vùng miền và cùng loại hình công trình") — mức độ ưu tiên và cách xử lý khi có nhiều bảng giá đã duyệt cho cùng đầu việc là khoảng trống, ghi ở `PL-C`.
- Đặc tả không nói rõ phụ lục (`-PLn`) có phải đi qua **cùng đủ 3 chặng phê duyệt** như bảng gốc hay một luồng rút gọn — UAT-16 giả định "giống bảng gốc" vì không có mục nào nói khác; đây là điểm cần chốt.
- Đặc tả không định nghĩa chi tiết **ma trận quyền** của "Theo luồng trình" (ai được xem, ai được thao tác từng chặng) — dựa vào PL-B.
- Liên màn hình: **BID-05** (BOQ hợp đồng), **BID-03** (giá vốn nhân công dự thầu), **PRJ-02** (sản lượng thi công), **PRJ-03** (hồ sơ giao khoán tiêu thụ bảng giá này), **ACC-09** (giá thành).
