# ACC-02 · Hệ thống kho — Kịch bản kiểm thử

> Nguồn: `spec/ACC-02.html`. Bao phủ cây kho, nghiệp vụ cho phép (hàng rào cứng), kho tổng là kho kế toán, định mức tồn, đóng kho, nhiều dự án chung một kho vật lý.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC02-1 | Có 4 dự án đang thi công, 6 dự án đã quyết toán | Menu **Kho → Danh mục kho** | Mặc định chỉ hiện kho tổng + kho của 4 dự án đang thi công + kho nhà máy; kho dự án đã quyết toán không xuất hiện |
| UAT-ACC02-2 | Đang ở danh mục kho | Bấm tab **Kho dự án (đang thi công)** | Danh sách lọc còn kho nhóm dự án của các dự án chưa quyết toán |
| UAT-ACC02-3 | Mở cấu hình kho `K-NVL` | Xem panel cấu hình | "Nghiệp vụ cho phép" là danh sách chọn nhiều; cờ "Trích khấu hao khi ở kho này" chỉ bật cho `K-TONG-CTY` |
| UAT-ACC02-4 | `K-NVL` chỉ khai "nhập mua" + "xuất SX" | Lập phiếu xuất từ `K-NVL`, mở ô **Mục đích xuất** | Danh sách chỉ có "sản xuất"; "bán" / "thi công" / "hao hụt" không hiện — chặn ngay trên giao diện, không hiện rồi báo lỗi |
| UAT-ACC02-5 | Bảng **Định mức tồn tối thiểu**, "Keo chà ron" tồn dưới `min_stock` | Xem bảng | Dòng tô cảnh báo và gắn nhãn "Dưới ĐM" |
| UAT-ACC02-6 | Kho dự án còn tồn 40 m² | Bấm **Đóng kho** | Hộp thoại chặn, hiển thị giá trị tồn và nút mở luồng điều chuyển |
| UAT-ACC02-7 | Hồ sơ kho tổng `K-TONG-CTY` | Xem trường **Vị trí vật lý** | Hiển thị "Nhà máy An Bình 2", tách biệt với **Đơn vị chủ quản** |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC02-8 | Vừa khởi tạo `DA-2026-020` ở BID-05 | Mở danh mục kho | Có kho `K-DA-2026-020` sinh tự động, thủ kho để trống chờ gán, mã suy từ mã dự án |
| UAT-ACC02-9 | `DA-2025-032` vừa quyết toán xong | Mở danh mục kho | Kho `K-DA-2025-032` tự chuyển trạng thái đóng, biến khỏi danh sách mặc định |
| UAT-ACC02-10 | `K-NVL` chỉ cho "nhập mua" và "xuất SX" | Lập phiếu xuất bán từ `K-NVL` | Nghiệp vụ "bán" bị chặn — không có trong danh sách chọn |
| UAT-ACC02-11 | Tài sản `TS-0012` ở kho tổng | Xem hồ sơ tài sản | Vị trí kế toán là kho tổng; vị trí vật lý ghi "Nhà máy An Bình 2"; ngày ở kho tổng trích khấu hao vào chi phí chung |
| UAT-ACC02-12 | Kho dự án còn tồn 40 m² | Bấm **Đóng kho** | Bị chặn kèm giá trị tồn và gợi ý luồng điều chuyển / hoàn nhập |
| UAT-ACC02-13 | Kho vật lý gắn `DA-A` và `DA-B` | Lập phiếu xuất từ kho này | Bắt buộc chọn dự án; báo cáo nhập–xuất–tồn vẫn tách được theo từng dự án |
| UAT-ACC02-14 | Khai một kho mới | Thử đặt `costing_method` khác bình quân gia quyền | Không có tùy chọn — phương pháp tính giá xuất áp chung toàn hệ thống |
| UAT-ACC02-15 | Khai kho nhóm "dự án" | Bỏ trống `project_id`, lưu | Chặn "Kho thuộc nhóm dự án phải gắn ít nhất một mã dự án." |
| UAT-ACC02-16 | `K-NVL` đã phát sinh nhiều chứng từ | Thử xóa kho | Chặn "Kho đã phát sinh {n} chứng từ, không xóa được. Chọn Đóng kho để ngừng sử dụng." |
| UAT-ACC02-17 | "Keo chà ron" xuống dưới `min_stock` 80 thùng | Ghi sổ phiếu xuất làm tồn giảm | Sinh cảnh báo (PF-04) cho thủ kho và P. Cung ứng |
| UAT-ACC02-18 | Đá dư từ `DA-2026-014` hoàn nhập về `K-CT` | Xem bản ghi tồn tại `K-CT` | Giữ tham chiếu dự án nguồn; khi nhà máy mua lại, giá trị ghi nhận đúng về `DA-2026-014` |

## Ghi chú bao phủ

- BR-01…BR-08 của đặc tả đều có kịch bản nhóm B.
- 5 dòng bảng *Kiểm tra dữ liệu & thông điệp* kiểm ở UAT-ACC02-10, -12, -15, -16; dòng "định mức vượt sức chứa" phụ thuộc **PL-C Q-17** (chưa có trường sức chứa) — chưa đưa kịch bản.
- Liên màn hình: **BID-05** (tự tạo kho), **ACC-03 / ACC-04** (nghiệp vụ cho phép), **ACC-06** (tài sản tại kho tổng), **PRJ-04** (kho dự án).
