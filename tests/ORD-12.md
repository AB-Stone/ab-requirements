# ORD-12 · Nhân công tổ đội — Kịch bản kiểm thử

> Nguồn: `spec/ORD-12.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.
>
> Thay bộ kịch bản cũ của PRJ-02 (Sản lượng thi công) và PRJ-03 (Giao khoán nhân công) từ 17/11/2026 — PL-C Q-344. Màn hình không có vòng duyệt và không có trạng thái; thứ duy nhất ghi sau khi lưu là “đã gửi Kế toán”.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-ORD12-1 | P. Đấu thầu vừa khởi tạo dự án mới từ hợp đồng | KS TQT mở Nhân công tổ đội | Dự án mới có trong danh sách, số bảng 0, kỳ gần nhất trống | TS lớp ① · BR-02 · KQ-1 |
| UAT-ORD12-2 | Dự án `HCM_0044` có hai bảng | Bấm vào dự án | Hai bảng hiện mới nhất lên đầu, có người lập, ngày lập, tổng thanh toán, cột “Đã gửi KT lúc” | TS lớp ② · KQ-2 |
| UAT-ORD12-3 | Dự án chưa có bảng | Bấm Lập bảng | Người lập là người đang đăng nhập (thuộc P. TQT), ngày lập hôm nay, tháng và năm theo ngày lập; đầu cột hai tầng, dòng TỔNG CỘNG bằng 0 | TS đầu bảng · KQ-3 |
| UAT-ORD12-4 | Chọn kỳ `khác` | Xem đầu bảng | Hiện ô nhãn kỳ; nhập “Bổ sung tết” thì đầu bảng in “Bổ sung tết tháng 12” | CN-1 · TS `period_label` |
| UAT-ORD12-5 | Tổ `TD-004` trong danh mục có số TK và ngân hàng | Gõ “TD-004” ở ô tên tổ đội, chọn | Số TK và ngân hàng điền sẵn, sửa được trên dòng | CN-2 · BR-05 · KQ-4 |
| UAT-ORD12-6 | Gõ “Tổ Đức Linh”, không có trong danh mục | Rời ô | Tên giữ nguyên, dòng gắn nhãn “gõ tay”, gợi ý “chưa có trong Danh mục tổ đội — chỉ lưu trên bảng này” | CN-2 · VAL-7 |
| UAT-ORD12-7 | Dự án đã có bảng Kỳ 1 tháng 12/2026 | Lập bảng mới cùng kỳ | Thẻ cảnh báo trùng kỳ trên thanh tiêu đề, bấm vào mở bảng kia | CN-3 · VAL-4 |
| UAT-ORD12-8 | Một dòng có C = 0, G = 500.000 | Rời ô G | H = −500.000 hiện màu cảnh báo kèm thông điệp | CN-4 · VAL-6 |
| UAT-ORD12-9 | Kế toán viên đăng nhập | Mở một bảng | Xem và xuất được; không có nút Lập bảng, Lưu, Xóa, Gửi Kế toán | BR-12 · VAL-9 · KQ-10 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-ORD12-10 | Bảng mới của `HCM_0044` | Thêm dòng LÊ KIM TÀI: E = 335.000, F = 12.790.000, các ô khác 0 | H = 13.125.000; TỔNG CỘNG cột H = 13.125.000 — đúng dòng mẫu sheet “nhancong” | BR-03 · KQ-5 |
| UAT-ORD12-11 | Tiếp UAT-10 | Thêm dòng C = 85.400.000, F = 3.600.000, G = 1.200.000 | H = 87.800.000 (G trừ, F cộng); TỔNG CỘNG H = 100.925.000, cột G = 1.200.000 | BR-03 |
| UAT-ORD12-12 | Tiếp UAT-11 | Thêm dòng C = 42.150.000, D = 2.400.000 | H = 44.550.000 (D cộng); TỔNG CỘNG C 127.550.000 · D 2.400.000 · E 335.000 · F 16.390.000 · G 1.200.000 · H 145.475.000 | BR-03 · TS dòng TỔNG CỘNG |
| UAT-ORD12-13 | Dự án chưa có bảng giá khoán (PRJ-01), vùng chưa có đơn giá chuẩn (PF-06) | Lập và lưu bảng | Lưu thành công, không có thông điệp nào về bảng giá hay đơn giá chuẩn | BR-04 · KQ-6 |
| UAT-ORD12-14 | Tiếp UAT-6 | Lưu bảng, mở Danh mục tổ đội (PRJ-05) | Dòng lưu với `crew_id` trống; danh mục không có thêm bản ghi “Tổ Đức Linh” | BR-05 |
| UAT-ORD12-15 | Tiếp UAT-5, đã sửa số TK trên dòng | Lưu, mở Danh mục tổ đội | Danh mục giữ số TK cũ; bảng giữ số đã sửa | BR-05 |
| UAT-ORD12-16 | Tiếp UAT-7 | Lưu | Bảng mới lưu được; dự án có hai bảng cùng Kỳ 1 tháng 12/2026 | BR-07 · KQ-7 |
| UAT-ORD12-17 | Kỳ `khác`, nhãn trống | Lưu | Bị chặn “Nhập tên kỳ cho kỳ khác.” | VAL-2 |
| UAT-ORD12-18 | Dòng có số tiền, ô tên tổ đội trống | Lưu | Bị chặn ở dòng đó “Dòng {stt} chưa có tên tổ đội.” | VAL-1 |
| UAT-ORD12-19 | Bảng ở UAT-12, chưa gửi | Bấm Gửi Kế toán | Cột “Đã gửi KT lúc” có thời điểm và người gửi; mọi tài khoản thuộc P. Kế toán nhận `crew_labor_sent` | BR-09 · KQ-8 · PF-04 |
| UAT-ORD12-20 | Tiếp UAT-19 | Sửa một số tiền, lưu, gửi lại | Sửa được; lớp ② hiện “đã sửa sau khi gửi” tới lần gửi lại; P. Kế toán nhận thông báo thứ hai có “(gửi lại)” | BR-09 · CN-6 |
| UAT-ORD12-21 | Tiếp UAT-19 | Kế toán mở danh sách đề nghị thanh toán (ACC-05) | Không có đề nghị nào tự sinh từ bảng | BR-09 · BR-10 |
| UAT-ORD12-22 | Bảng chưa có dòng | Bấm Gửi Kế toán | Bị chặn “Bảng chưa có dòng tổ đội nào để gửi Kế toán.” | VAL-8 |
| UAT-ORD12-23 | Bảng ở UAT-12 | Xuất .xlsx | Một sheet theo mẫu “nhancong”: đầu bảng, tiêu đề, đầu cột hai tầng với hàng A–H, ba dòng, TỔNG CỘNG; cột H là công thức và ra đúng số trên màn hình | CN-5 · BR-11 · KQ-9 |
| UAT-ORD12-24 | Kế toán viên gọi thẳng API lưu bảng | Gửi yêu cầu | Máy chủ trả 403 | BR-12 · VAL-9 |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → (thay PRJ-02/03, không có hành vi giao diện riêng) · BR-02 → UAT-1 · BR-03 → UAT-10, UAT-11, UAT-12 · BR-04 → UAT-13 · BR-05 → UAT-5, UAT-14, UAT-15 · BR-06 → VAL-5 (chưa có kịch bản riêng) · BR-07 → UAT-16 · BR-08 → UAT-19 · BR-09 → UAT-19, UAT-20, UAT-21 · BR-10 → UAT-21 · BR-11 → UAT-23 · BR-12 → UAT-9, UAT-24.
- **Kết quả mong đợi**: KQ-1 → UAT-1 · KQ-2 → UAT-2 · KQ-3 → UAT-3 · KQ-4 → UAT-5 · KQ-5 → UAT-10 · KQ-6 → UAT-13 · KQ-7 → UAT-16 · KQ-8 → UAT-19 · KQ-9 → UAT-23 · KQ-10 → UAT-9. Đủ 10/10.
- Đặc tả không nói xóa một bảng đã gửi Kế toán có báo lại P. Kế toán hay không — chỉ ghi “hỏi xác nhận, nhật ký giữ lần gửi” (BR-12).
- Liên màn hình: **PRJ-05** (danh mục tổ đội, số TK, ngân hàng), **BID-05 / PF-01** (nguồn dự án), **PF-04** (`crew_labor_sent`), **ACC-05** (đề nghị thanh toán dòng nhập tay, không tự sinh), **PRJ-01 / PF-06** (không còn là điều kiện).
