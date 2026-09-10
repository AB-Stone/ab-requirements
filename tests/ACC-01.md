# ACC-01 · Đề xuất mua sắm tài sản, CCDC, vật tư & chi phí — Kịch bản kiểm thử

> Nguồn: `spec/ACC-01.html`. Bao phủ 3 loại đề xuất, chế độ nhập theo dòng, duyệt 1 cấp / nhiều cấp, lũy kế nhập từ phiếu nhập kho, ngưỡng lệch 5%.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC01-1 | Đăng nhập vai trò Ban dự án / Kế toán | Menu **Đề xuất → Lập đề xuất** | Form mở ở trạng thái *nháp*, mã `DX-YYYY-NNNN` tự sinh, không nhập tay được |
| UAT-ACC01-2 | Form đề xuất mới | Chọn **Loại đề xuất = Loại 1** | Bảng dòng hiển thị đủ cột theo dõi NXT (ĐVT, SL, đơn giá); chọn **Loại 3** thì cột SL và đơn giá ẩn, chỉ còn *nội dung* + *thành tiền* |
| UAT-ACC01-3 | Đề xuất có sẵn 1 dòng "có số lượng" | Đổi **Chế độ** của dòng đó sang "chỉ giá trị" | Ô ĐVT / SL / đơn giá của dòng khóa mờ, chỉ còn *thành tiền* nhập được; các dòng khác không đổi |
| UAT-ACC01-4 | Đề xuất Loại 2, giá trị 800 triệu | Mở khối **Cấp duyệt áp dụng** | Hiển thị trước 3 bước *GĐ dự án → Kế toán trưởng → Tổng GĐ* trước khi bấm Trình duyệt |
| UAT-ACC01-5 | Đề xuất đã duyệt, có 2 phiếu nhập kho tick về nó | Mở đề xuất, xem cột **Đã nhập** | Dòng có phiếu nhập hiển thị "24 / 40"; dòng chưa nhập hiển thị "0 / n" — số cộng từ phiếu nhập kho, không sửa tay |
| UAT-ACC01-6 | Đề xuất nhập 60% khối lượng | Xem thanh trạng thái phiếu | Tag "Nhập một phần · 60%" và dòng cảnh báo "Còn 16 lưỡi cắt chưa nhập" |
| UAT-ACC01-7 | Form đề xuất thiếu trường **Nơi thực hiện / cung cấp** | Bấm **Trình duyệt** | Thông điệp "Thiếu Nơi cung cấp. Người duyệt cần đủ ba thông tin này để quyết định."; đề xuất không được gửi đi |
| UAT-ACC01-8 | Đề xuất chỉ đính kèm tệp PDF, chưa nhập dòng nào | Bấm **Trình duyệt** | Hộp xác nhận "Đề xuất chỉ có tệp đính kèm sẽ không tự đối chiếu được với phiếu nhập kho. Xác nhận để tiếp tục." |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC01-9 | Đề xuất Loại 2, giá trị 28 triệu | Thêm 2 dòng "có số lượng" và 1 dòng "chỉ giá trị" | Cả ba dòng lưu được trên cùng một phiếu |
| UAT-ACC01-10 | Đề xuất Loại 1, giá trị 150 triệu | Trình duyệt | Cả GĐ dự án và Kế toán trưởng thấy trong hàng chờ; **một người** duyệt là chuyển bước, người kia thấy "đã xử lý bởi …" và chứng từ rời hàng chờ |
| UAT-ACC01-11 | Đề xuất Loại 2, giá trị 800 triệu | Trình duyệt | Đi tuần tự GĐ dự án → Kế toán trưởng → Tổng GĐ; bỏ qua bất kỳ cấp nào thì không chuyển bước |
| UAT-ACC01-12 | Hạ ngưỡng hạn mức ở **PF-03** | Lập đề xuất Loại 2 cùng giá trị như trước | Đề xuất đi theo số cấp mới, không cần sửa phần mềm |
| UAT-ACC01-13 | Đề xuất 40 lưỡi cắt, đã nhập 24 | Xem đề xuất | Trạng thái "nhập một phần", hiện còn 16 theo từng dòng |
| UAT-ACC01-14 | Tiếp UAT-ACC01-13 | Lập phiếu nhập 16 lưỡi, tick về đề xuất, ghi sổ | Đề xuất tự chuyển "hoàn thành" và rời nhóm đang theo dõi |
| UAT-ACC01-15 | Đề xuất 100 bao xi, nhà cung cấp giao 112 | Lập phiếu nhập 112 | Lệch 12% > 5% → phiếu nhập yêu cầu phê duyệt bổ sung của cấp đã duyệt đề xuất trước khi ghi sổ |
| UAT-ACC01-16 | Đề xuất 100 bao xi, nhà cung cấp giao 103 | Lập phiếu nhập 103, ghi sổ | Lệch 3% ≤ 5% → cho ghi sổ, chỉ ghi chú, không cần duyệt lại |
| UAT-ACC01-17 | Nhà cung cấp ngừng giao phần còn lại | Ban dự án bấm **Đóng sớm**, nhập lý do | Phần chưa nhập ghi nhận "hủy"; đề xuất rời nhóm đang theo dõi, không treo vô thời hạn |
| UAT-ACC01-18 | Đề xuất Loại 1 đã ở trạng thái *đã duyệt* | Thử đổi sang Loại 2 | Bị chặn "Loại đề xuất đã khóa sau khi duyệt vì ảnh hưởng cách ghi nhận chi phí. Lập đề xuất mới nếu cần đổi." |
| UAT-ACC01-19 | Phiếu nhập kho Loại 1 tick về đề xuất | Ghi sổ phiếu nhập | Chi phí ghi về dự án theo **giá trước VAT** tại thời điểm nhập kho; phần VAT tách riêng, không vào giá vốn |

## Ghi chú bao phủ

- Quy tắc nghiệp vụ BR-01…BR-08 của đặc tả đều có ít nhất một kịch bản nhóm B.
- 6 dòng bảng *Kiểm tra dữ liệu & thông điệp* được kiểm ở UAT-ACC01-7, -8, -18 và -15 (ngưỡng lệch).
- Liên màn hình cần chạy tiếp: **ACC-03** (phiếu nhập cập nhật lũy kế), **ACC-05** (phát sinh phải trả), **ACC-06** (đề xuất Loại 2 ghi tăng tài sản), **PF-03** (cấu hình cấp duyệt).
