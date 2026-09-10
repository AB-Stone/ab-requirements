# ACC-03 · Mua hàng & nhập kho — Kịch bản kiểm thử

> Nguồn: `spec/ACC-03.html`. Bao phủ 3 hình thức mua, hai hệ đơn vị tính, VAT tách khỏi giá vốn, chi phí vận chuyển hai đường, giảm giá hàng mua, nhập chưa có hóa đơn, phiếu không có đề xuất.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC03-1 | Vai trò Kế toán kho | Menu **Kho → Phiếu nhập kho → + Phiếu nhập** | Form mở, `PN-YYYY-NNNN` tự sinh; 4 trường quản trị (dự án, KMCP, mã thống kê, tick CP hợp lý) nằm ở đầu form |
| UAT-ACC03-2 | Form phiếu nhập mới | Mở ô **Kho nhập** | Chỉ liệt kê kho có `allowed_ops` chứa "nhập mua"; `K-TONG-CTY` không xuất hiện |
| UAT-ACC03-3 | Vật tư có hệ số PF-01: 1 m² = 2,78 tấm | Nhập **SL kế toán** = 1.240 m² | Cột **SL dự án** tự điền 3.444 tấm |
| UAT-ACC03-4 | Tiếp UAT-ACC03-3 | Sửa tay ô **SL dự án** lệch hệ số chuẩn | Ô gắn cờ cảnh báo lệch quy đổi; vẫn cho lưu |
| UAT-ACC03-5 | Khối **Chi phí vận chuyển** | Chọn loại = "Vận chuyển về nhà máy" | Mở bảng tick chọn nhiều phiếu nhập cùng chuyến; chọn "ra dự án" thì bảng phân bổ ẩn, hiện dòng "ghi TK chi phí / 331" |
| UAT-ACC03-6 | Có đề xuất `DX-2026-0392` đang nhập một phần | Tick **Đề xuất nguồn** = `DX-2026-0392` | Dòng hiển thị "nhập một phần"; sau khi ghi sổ, lũy kế đề xuất tăng tương ứng |
| UAT-ACC03-7 | Phiếu nhập chưa đính kèm hóa đơn | Ghi sổ | Thanh trạng thái hiển thị tag "Chờ hóa đơn"; phiếu vào báo cáo theo dõi |
| UAT-ACC03-8 | Có tệp Excel thành phẩm theo mẫu | Bấm **Nhập từ Excel** | Wizard: tải mẫu → đối chiếu cột → bảng lỗi theo dòng; nút **Ghi sổ** mờ khi còn dòng lỗi |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng |
|---|---|---|---|
| UAT-ACC03-9 | Đề xuất Loại 1 đã duyệt | Lập phiếu nhập tick về đề xuất, ghi sổ | Chi phí ghi về dự án theo **giá trước VAT** tại thời điểm nhập kho; lũy kế đề xuất tăng |
| UAT-ACC03-10 | Vật tư hệ số 1 m² = 2,78 tấm | Nhập 1.240 m² ở cột kế toán, ghi sổ | Tồn dự án ghi 3.444 tấm; giá xuất kho tính theo hệ ĐVT kế toán |
| UAT-ACC03-11 | Chi phí vận chuyển 64 triệu cho 2 phiếu nhập cùng chuyến | Chọn "về nhà máy", tick cả hai phiếu | Phân bổ theo số lượng, cộng vào **giá vốn** từng phiếu |
| UAT-ACC03-12 | Cùng số tiền 64 triệu | Chọn "ra dự án", ghi sổ | Ghi Nợ TK chi phí / Có 331; **giá vốn hàng không đổi** |
| UAT-ACC03-13 | Lô hàng đã nhập, đã xuất 1 phiếu | Ghi nhận giảm giá hàng mua 36,7 triệu | Giá xuất kho bình quân tính lại cho các lần xuất **sau**; phiếu xuất đã ghi sổ giữ nguyên giá vốn cũ |
| UAT-ACC03-14 | Nhận hàng chưa có hóa đơn | Ghi sổ phiếu nhập | Phiếu gắn cờ "chờ hóa đơn", xuất hiện trong báo cáo theo dõi riêng |
| UAT-ACC03-15 | Phiếu "chờ hóa đơn" đã quá 30 ngày | Chạy nhắc việc định kỳ | Hệ thống nhắc kế toán và trưởng bộ phận mua hàng |
| UAT-ACC03-16 | Không có đề xuất nào phù hợp | Lập phiếu nhập, chọn lý do "mua khẩn cấp tại công trình" | Phiếu vào luồng duyệt của Kế toán trưởng; xuất hiện trong báo cáo phiếu không đề xuất |
| UAT-ACC03-17 | Đơn giá nhập lệch > 20% so với lần nhập gần nhất | Nhập đơn giá, rời ô | Cảnh báo "Đơn giá {giá} lệch {x}% … Kiểm tra lại đơn vị tính và quy cách."; cho tiếp tục sau xác nhận |
| UAT-ACC03-18 | Chứng từ giảm giá lớn hơn giá trị lô hàng | Nhập giá trị giảm giá | Chặn "Giá trị giảm giá vượt giá trị lô hàng. Kiểm tra lại chứng từ giảm giá." |
| UAT-ACC03-19 | Tệp Excel thành phẩm thiếu cột quy cách | Tải lên | Báo lỗi nêu tên cột thiếu; không ghi sổ dòng nào |

## Ghi chú bao phủ

- BR-01…BR-08 đều có kịch bản nhóm B; BR "chi phí tại thời điểm nhập kho, trước VAT" kiểm ở UAT-ACC03-9.
- 6 dòng bảng *Kiểm tra dữ liệu & thông điệp* kiểm ở UAT-ACC03-5 (loại vận chuyển), -16, -17, -18, -19.
- Liên màn hình: **ACC-01** (lũy kế đề xuất), **ACC-02** (kho cho nhập mua), **ACC-04** (giá xuất kho bình quân), **ACC-05** (phát sinh phải trả), **ACC-06** (ghi tăng tài sản, hình thức không qua kho).
