# ACC-04 · Xuất kho & bán hàng — Kịch bản kiểm thử

> Nguồn: `spec/ACC-04.html`. Bao phủ 4 mục đích xuất → 4 bộ định khoản, xuất sản xuất miễn duyệt, giá xuất kho là số hệ thống, vòng kiểm nhận của dự án, trả hàng lỗi.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC04-1 | Vai trò Kế toán kho | Menu **Kho → Phiếu xuất kho → + Phiếu xuất** | Hai trường **Kho xuất** + **Mục đích xuất** hiện trước; các trường còn lại phụ thuộc lựa chọn của hai trường này |
| UAT-ACC04-2 | Form phiếu xuất mới | Chọn **Mục đích = "Bán"** | Khối **Định khoản áp dụng** hiện Nợ 632 / Có 152, 156 và Nợ 1311 / Có 5113; xuất hiện trường **Tình trạng hóa đơn** |
| UAT-ACC04-3 | Tiếp UAT-ACC04-2 | Đổi **Mục đích = "Thi công"** | Khối định khoản đổi sang "điều chuyển nội bộ · ghi chi phí khi kho DA xuất cho tổ đội"; ẩn "Tình trạng hóa đơn", **Dự án nhận** thành bắt buộc |
| UAT-ACC04-4 | Tiếp | Đổi **Mục đích = "Sản xuất"** | Thanh nút đổi thành **Ghi sổ** (không còn "Duyệt xuất"); chú thích "ghi sổ ngay, không qua phê duyệt" |
| UAT-ACC04-5 | Mục đích = "Bán", đang chọn "đã xuất hóa đơn" | Đổi sang "chưa xuất hóa đơn" | Dòng phải thu trong khối định khoản đổi 1311 → 1312 |
| UAT-ACC04-6 | Dòng xuất, hệ số PF-01 quy đổi có sẵn | Nhập **SL kế toán** = 280 | **SL dự án** tự điền 778; **Thành tiền** = SL × giá xuất bình quân hệ thống tính |
| UAT-ACC04-7 | Dòng xuất đã có vật tư | Trỏ / bấm vào ô **Giá xuất BQ** | Ô chỉ đọc với mọi vai trò; tooltip nêu bình quân gia quyền và gợi ý dùng bút toán điều chỉnh |
| UAT-ACC04-8 | Phiếu xuất thi công đã duyệt | Bấm **In biên bản giao hàng** | Nút chỉ bật khi phiếu ở trạng thái đã duyệt; bản in dùng **ĐVT dự án**, số liệu khớp phiếu xuất |
| UAT-ACC04-9 | Phiếu ở trạng thái "Chờ dự án xác nhận" | Xem thanh vòng kiểm nhận | 4 mốc hiển thị đúng: Duyệt xuất ✓ / Đã thông báo ✓ / Dự án kiểm nhận (đang chờ) / Xác nhận (—) |
| UAT-ACC04-10 | Xuất hao hụt, chưa đính kèm biên bản nguyên nhân | Bấm **Trình duyệt** | Thông điệp "Xuất hao hụt cần biên bản xác định nguyên nhân."; nút trình duyệt không gửi đi |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC04-11 | Kho `K-NVL` có tồn NVL | Lập phiếu xuất mục đích "sản xuất", lưu | Ghi sổ ngay Nợ 621 / Có 152; **không** tạo phiên phê duyệt nào |
| UAT-ACC04-12 | Kho `K-HH` có thành phẩm | Phiếu xuất "bán", chọn "đã xuất hóa đơn", ghi sổ | Sinh 2 cặp bút toán: Nợ 632 / Có 156 và Nợ 1311 / Có 5113 |
| UAT-ACC04-13 | Tiếp UAT-ACC04-12 | Đổi sang "chưa xuất hóa đơn", ghi sổ | Tài khoản phải thu đổi thành 1312; doanh thu 5113 và giá vốn 632 giữ nguyên |
| UAT-ACC04-14 | Phiếu xuất thi công được duyệt | Kiểm tra tồn kho và chi phí dự án | Tồn kho nhà máy giảm, kho dự án tăng; chi phí dự án **chưa** tăng (chỉ tăng khi PRJ-04 xuất cho tổ đội) |
| UAT-ACC04-15 | Phiếu xuất thi công vừa được duyệt | Kiểm tra thông báo phía dự án | Dự án nhận thông báo "có phiếu xuất hàng đi" ngay sau bước duyệt, trước khi hàng tới |
| UAT-ACC04-16 | Dự án kiểm nhận thấy vỡ 20 m² trên 280 | Ghi sai lệch và chọn **trả hàng** | Phiếu về nhà máy ở trạng thái sửa được; SL thực nhận 260; phải **duyệt lại từ đầu** sau khi cập nhật |
| UAT-ACC04-17 | Dòng xuất SL 300, tồn khả dụng tại kho 280 | Nhập 300 | Chặn "Chỉ còn 280 {đvt} khả dụng tại {kho}." |
| UAT-ACC04-18 | Xuất hao hụt 3% trên định mức nhóm vật tư 1% | Trình duyệt | Yêu cầu biên bản quy trách nhiệm trước khi ghi sổ |
| UAT-ACC04-19 | Kế toán trưởng mở phiếu đã ghi sổ | Thử sửa ô giá xuất kho | Ô chỉ đọc; chỉ điều chỉnh được qua bút toán riêng ở **ACC-08**, có dấu vết |

## Ghi chú bao phủ

- 4 bộ định khoản (sản xuất / bán / hao hụt / thi công) đều có kịch bản; ngoại lệ "sản xuất miễn duyệt" kiểm ở UAT-ACC04-4 và -11.
- 6 dòng bảng *Kiểm tra dữ liệu & thông điệp* kiểm ở UAT-ACC04-10, -17, -18, -19 và -5 (tình trạng hóa đơn), -3 (thi công thiếu dự án).
- Liên màn hình: **ACC-02** (nghiệp vụ cho phép của kho), **ACC-03** (giá nhập → giá xuất bình quân), **ORD-05** (cấp hàng theo đợt), **PRJ-04** (kho dự án ghi chi phí), **ACC-09** (giá thành).
