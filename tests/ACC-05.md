# ACC-05 · Công nợ & thanh toán — Kịch bản kiểm thử

> Nguồn: `spec/ACC-05.html`. Bao phủ sổ công nợ hai chiều, tuổi nợ 4 nhóm, đề nghị thanh toán (một chứng từ một đề nghị), duyệt theo hạn mức, thanh toán ghi 3 nơi, hạn mức công nợ khách hàng.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC05-1 | Menu **Công nợ**, mặc định tab "Phải thu khách hàng" | Bấm tab **Phải trả nhà cung cấp** | Bảng đổi chiều công nợ; các ô KPI đầu trang (tổng, trong hạn, quá hạn) cập nhật theo |
| UAT-ACC05-2 | Bảng tuổi nợ theo khách hàng | Bấm ô **> 60** của một khách hàng | Mở danh sách chứng từ thuộc nhóm quá hạn > 60 ngày của khách đó |
| UAT-ACC05-3 | Đang xem tuổi nợ | Chọn bộ lọc **Công trình** = `DA-2026-014` | Bảng tuổi nợ và tổng hợp công nợ lọc theo công trình, kèm dòng tổng hợp chung |
| UAT-ACC05-4 | Bấm **+ Đề nghị thanh toán** | Chọn đối tác = "Mỏ đá Phú Yên" | Hiện danh sách chứng từ gốc còn dư nợ của đối tác đó để tick chọn |
| UAT-ACC05-5 | Form đề nghị thanh toán | Chọn **Hình thức = "Chuyển khoản"** | Trường **Tài khoản nhận** xuất hiện và thành bắt buộc; chọn "Tiền mặt" thì trường ẩn |
| UAT-ACC05-6 | Đề nghị giá trị 1,84 tỷ | Mở khối **Cấp duyệt áp dụng** | Hiển thị "3 cấp — giá trị > 1 tỷ" trước khi bấm Trình duyệt |
| UAT-ACC05-7 | Có nhiều mặt hàng phát sinh trên chứng từ nguồn | Bấm **Xuất sổ chi tiết công nợ** | Tệp có cột tên hàng, đơn giá, số lượng — không chỉ số tiền |
| UAT-ACC05-8 | `PN-2026-0188` đã nằm trong `DNTT-2026-0271` | Tick chứng từ này khi lập đề nghị mới | Dòng khóa chọn, thông điệp "Chứng từ {mã} đã nằm trong đề nghị {mã}. Mở đề nghị đó để kiểm tra." |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC05-9 | Ghi sổ phiếu nhập 1,07 tỷ | Mở công nợ phải trả nhà cung cấp | Dư nợ tăng đúng 1,07 tỷ, không cần nhập tay |
| UAT-ACC05-10 | `PN-2026-0188` đã trong `DNTT-2026-0271` | Lập đề nghị mới chọn phiếu này | Bị chặn kèm mã đề nghị đã dùng |
| UAT-ACC05-11 | Đề nghị 1,84 tỷ | Trình duyệt | Đi qua 3 cấp vì vượt ngưỡng 1 tỷ; đổi hạn mức là thao tác cấu hình ở PF-03 |
| UAT-ACC05-12 | Đề nghị đã duyệt đủ cấp | Lập phiếu chi và ghi sổ | Trong một giao dịch: công nợ giảm + sổ quỹ / sổ ngân hàng ghi + sổ chi tiết đối tác cập nhật |
| UAT-ACC05-13 | Tiếp UAT-ACC05-12, giả lập lỗi khi ghi sổ ngân hàng | Ghi sổ phiếu chi | Cả 3 bút toán hoàn tác; công nợ và sổ chi tiết không thay đổi |
| UAT-ACC05-14 | Coteccons có dư nợ 800 triệu quá hạn > 60 ngày | Mở bảng tuổi nợ | Nằm ở nhóm "> 60 ngày", nhãn "Quá hạn nặng"; tính từ hạn thanh toán, không từ ngày phát sinh |
| UAT-ACC05-15 | Coteccons đang vượt hạn mức công nợ | Duyệt đơn hàng mới của Coteccons | Cảnh báo hiện trên màn hình duyệt; **vẫn cho duyệt** — quyết định thuộc người duyệt |
| UAT-ACC05-16 | Các chứng từ đã chọn có tổng dư nợ 1,5 tỷ | Nhập giá trị đề nghị 1,8 tỷ | Chặn "Đề nghị {giá trị} vượt dư nợ {giá trị} …"; muốn trả trước phải lập đề nghị tạm ứng |
| UAT-ACC05-17 | Số tài khoản nhận khác tài khoản đã đăng ký của đối tác | Trình duyệt | Yêu cầu xác nhận "Số tài khoản khác với tài khoản đã đăng ký của {đối tác}." trước khi trình |
| UAT-ACC05-18 | Các chứng từ đã chọn thuộc nhiều dự án | Ghi sổ đề nghị | Cảnh báo và đề nghị **tách dòng theo dự án** khi ghi sổ |
| UAT-ACC05-19 | Hồ sơ giao khoán nhân công vừa được duyệt (PRJ-03) | Mở danh sách đề nghị thanh toán | Đã có đề nghị tạo sẵn dữ liệu với giá trị thực trả đúng; kế toán chỉ kiểm và trình |

## Ghi chú bao phủ

- BR-01…BR-08 đều có kịch bản nhóm B; "thanh toán ghi ba nơi" kiểm ở UAT-ACC05-12, hoàn tác nguyên tử ở UAT-ACC05-13.
- 6 dòng bảng *Kiểm tra dữ liệu & thông điệp* kiểm ở UAT-ACC05-8/-10, -16, -17, -5 (thiếu TK), -15, -18.
- Liên màn hình: **ACC-03** (phát sinh phải trả), **ORD-01 / ORD-09** (phát sinh phải thu), **PRJ-03** (thanh toán tổ đội), **ACC-07** (sổ quỹ & ngân hàng), **RPT-01** (sổ chi tiết công nợ).
