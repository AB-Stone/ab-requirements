# BID-03 · Áp giá, gửi báo giá & thương thảo — Kịch bản kiểm thử

> Nguồn: `spec/BID-03.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `BTH` = mục *Bảng tổng hợp giá trị đơn hàng* · `XB` = mục *Xuất bản báo giá* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID03-1 | BOQ cập nhật đã chốt ở BID-02 | Mở màn hình áp giá | Bảng hiển thị đồng thời giá vốn, giá chào và biên lợi nhuận từng dòng, cùng dòng tổng ở chân bảng | KQ-1 · CN-2 |
| UAT-BID03-2 | Đang ở bảng áp giá | Thử sửa ô **Khối lượng** của một dòng | Cột khối lượng bị khoá — kế thừa từ BOQ cập nhật, muốn đổi phải quay lại BID-02 và chốt bản BOQ mới | BR-01 · TS · `quote_lines[]` |
| UAT-BID03-3 | Một dòng đã có giá vốn | Nhập đơn giá chào; sau đó ở một dòng khác nhập hệ số lợi nhuận | Nhập đơn giá thì hệ số tự tính; nhập hệ số thì đơn giá tự tính | BR-05 · CN-2 |
| UAT-BID03-4 | Ngưỡng biên tối thiểu 18%, có dòng dưới ngưỡng | Xem bảng áp giá | Dòng dưới ngưỡng tự tô đỏ và được đếm ở chân bảng | CN-2 · TS · `margin_pct` |
| UAT-BID03-5 | Đang ở màn hình áp giá | Mở trình chọn cấu phần giá vốn | Ba cấu phần có sẵn (vật tư · nhân công · máy & biện pháp) bật mặc định; khai thêm được tối đa 8 cấu phần riêng | CN-6 · TS · `cost_components[]` |
| UAT-BID03-6 | Tiếp UAT-BID03-5 | Bật một cấu phần đã khai | Cột trên bảng áp giá đổi theo ngay | CN-6 |
| UAT-BID03-7 | Vòng báo giá đã gửi đối tác | Xem khối chốt vòng ngay dưới bảng áp giá | Dải bước của vòng — áp giá → duyệt giá → gửi đối tác → phản hồi → chốt — kèm đúng một hành động của bước hiện tại; ba cách kết thúc nằm cùng một chỗ, mỗi cách in sẵn hệ quả của nó | KQ-9 · KQ-10 · CN-4 |
| UAT-BID03-8 | Vòng đang ở biên dưới ngưỡng | Mở màn hình áp giá | Dải bước của vòng đó có thêm bước **Duyệt giá**; nút gửi khoá và hành động của bước hiện tại là trình Ban lãnh đạo | BR-12 · CN-4 |
| UAT-BID03-9 | Gói đã đi nhiều vòng và đã chốt ở một vòng | Mở lịch sử thương thảo | Dòng thời gian gửi v1 → phản hồi → gửi v2 → chốt, kèm chênh lệch giá trị từng vòng; chỉ vòng chốt mang nhãn "Giá chốt"; khối này chỉ đọc, mọi thao tác kết thúc vòng nằm ở khối chốt vòng | KQ-6 · CN-3 |
| UAT-BID03-10 | Một vòng đã áp giá, BOQ đã khai đủ thuộc tính đá | Mở bảng tổng hợp giá trị đơn hàng | Mở được mà không rời màn hình áp giá; đủ 14 cột kèm khối định danh và khối ký duyệt; bảng chỉ đọc, dòng tổng bằng đúng `total_offer` của vòng | KQ-7 · BTH |
| UAT-BID03-11 | Gói thầu chưa có hợp đồng | Xem khối định danh của bảng tổng hợp | Ô **HĐ số** hiện "Chưa có HĐ" thay vì bỏ trống | BTH |
| UAT-BID03-12 | Gói thầu không phải hạng mục đá, không khai thuộc tính đá | Mở bảng đầu việc và bảng tổng hợp | Sáu cột thuộc tính đá không hiện; bảng không có cột trống toàn dấu gạch | Kịch bản kiểm thử của đặc tả · UAT-BID03-9 |
| UAT-BID03-13 | Một vòng đã trình duyệt xong | Bấm xuất bản báo giá | Chọn được vòng và định dạng (PDF · Excel) | CN-5 · XB |
| UAT-BID03-14 | Người dùng là nhân viên P. TQT | Mở màn hình áp giá | Không truy cập được — quyền chỉ dành cho Ban lãnh đạo, P. Kế toán và P. Đấu thầu | Quyền truy cập (PL-B) · Kịch bản kiểm thử của đặc tả · UAT-BID03-7 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID03-15 | BOQ cập nhật đã chốt | Sửa hệ số lợi nhuận ở dòng tổng | Mọi dòng chi tiết chưa bị khoá tay cập nhật theo | BR-05 · KQ-3 |
| UAT-BID03-16 | Một dòng đã sửa tay đơn giá | Đổi hệ số lợi nhuận ở dòng tổng | Dòng đã sửa tay giữ nguyên; dòng bị sửa tay được đánh dấu để người duyệt biết con số không còn là số hệ thống tính | BR-05 · BR-02 |
| UAT-BID03-17 | Biên lợi nhuận tổng đang trên ngưỡng | Giảm giá để biên tổng rơi dưới ngưỡng tối thiểu | Nút **Gửi đối tác** bị khoá; hiện "Biên lợi nhuận {x}% dưới ngưỡng tối thiểu {y}%. Cần Ban lãnh đạo duyệt mức giá này trước khi gửi đối tác." | BR-06 · VAL-2 · KQ-5 |
| UAT-BID03-18 | Tiếp UAT-BID03-17, Ban lãnh đạo đã duyệt chính mức giá đó | Bấm **Gửi đối tác** | Gửi được; không có đường vòng nào khác mở khoá ngoài phê duyệt cho chính mức giá đó | BR-06 |
| UAT-BID03-19 | Đã gửi một vòng | Mở bảng giá vòng đó và thử sửa | Bảng đã đóng băng tại thời điểm gửi, không sửa được; thao tác sửa tạo bản nháp của vòng kế tiếp | BR-07 · KQ-4 |
| UAT-BID03-20 | Gói thuần cung cấp vật tư, không có phần nhân công | Tắt cấu phần "Nhân công", khai thêm "Vận chuyển" rồi nhập số | Bảng bỏ cột nhân công và hiện cột vận chuyển; giá vốn dòng bằng tổng các cấu phần đang bật cộng chi phí chung phân bổ; biên lợi nhuận tính lại | BR-02 · BR-03 · KQ-2 |
| UAT-BID03-21 | Một cấu phần đang có số khác 0 ở nhiều dòng | Tắt cấu phần đó | "Cấu phần {tên} đang có số ở {n} dòng. Xoá số ở các dòng đó trước khi tắt cột." Xoá hết số rồi tắt lại thì được | VAL-5 · BR-03 |
| UAT-BID03-22 | Phiếu khảo sát BID-02 đang chuyển sang một khoản chi phí biện pháp | Tắt cấu phần "Máy & biện pháp" | "Phiếu khảo sát đang chuyển sang {số tiền} chi phí biện pháp thi công khó. Không tắt được cấu phần này." | VAL-6 · BR-03 |
| UAT-BID03-23 | Chỉ còn một cấu phần đang bật | Tắt nốt cấu phần đó | "Phải giữ ít nhất một cấu phần giá vốn — không có giá vốn thì không tính được biên lợi nhuận." | VAL-7 · BR-03 |
| UAT-BID03-24 | Gói đã khai 8 cấu phần riêng | Khai thêm cấu phần nữa | "Một gói thầu khai thêm được tối đa 8 cấu phần giá vốn." | VAL-8 · TS · `cost_components[]` |
| UAT-BID03-25 | Một cấu phần tự khai đã nhập số | Bỏ hẳn cấu phần đó, rồi khai lại cùng tên | Có cảnh báo trước khi lưu; số liệu bị xoá; khai lại cùng tên chỉ ra một cột trống | BR-03 |
| UAT-BID03-26 | Một vòng đã gửi với bộ cấu phần cũ | Đổi bộ cấu phần cho bản nháp vòng kế tiếp, rồi mở lại vòng đã gửi | Vòng đã gửi giữ nguyên bộ cột và số liệu tại thời điểm gửi; so sánh giữa hai vòng đối chiếu ở mức tổng giá vốn và tổng giá chào kèm ghi chú "bộ cấu phần đã đổi" | BR-04 |
| UAT-BID03-27 | Một dòng có đơn giá chào thấp hơn giá vốn | Lưu bảng giá | "Dòng {mã} đang chào dưới giá vốn ({chênh lệch}). Nhập lý do nếu đây là chủ đích." | VAL-1 |
| UAT-BID03-28 | Cấu phần nhân công đang bật, một dòng để trống giá vốn nhân công | Trình duyệt giá | "Chưa có giá vốn nhân công cho dòng {mã}. Không có số này thì biên lợi nhuận không phản ánh đúng." | VAL-3 · TS · `cost_labor` |
| UAT-BID03-29 | Bảng giá không đổi so với vòng trước | Bấm **Gửi đối tác** | "Bảng giá không thay đổi so với vòng {n}. Xác nhận nếu chỉ gửi lại để nhắc đối tác." | VAL-4 |
| UAT-BID03-30 | Gói mới, ba cấu phần có sẵn đang bật | Mở bảng áp giá | Vật tư gợi ý từ giá nhập kho bình quân gần nhất; nhân công gợi ý từ bảng giá khoán tổ đội đã duyệt của dự án tương tự (PRJ-01); máy & biện pháp nạp từ phiếu khảo sát BID-02 | BR-02 · TS · `cost_material`, `cost_labor`, `cost_method` |
| UAT-BID03-31 | Mã liên kết thực xuất đã khai trên dòng BOQ ở BID-01 | Mở bảng áp giá vòng 1, vòng 2, rồi BOQ hợp đồng | Mã chảy xuống sẵn ở cả áp giá và hợp đồng — không nhập lại theo từng vòng | TS · `material_code` |
| UAT-BID03-32 | Ô **Sai số** của một dòng | Lần lượt gõ `3`, `+3`, `±3`, rồi dán `3` từ Excel; sau đó xoá trắng ô | Cả bốn cách vào cùng một sai số `±3`; ô để trống lưu null, không lưu chuỗi rỗng | TS · `tolerance` |
| UAT-BID03-33 | Ô **Chủng loại đá** và ô **Tính chất bề mặt** | Gõ một loại đá chưa có trong danh sách gợi ý; mở danh sách bề mặt | Chủng loại đá nhận giá trị mới (danh sách gợi ý nhưng không đóng); bề mặt là danh mục đóng đúng sáu giá trị: Mài bóng · Mài hone · Băm mặt · Khò nhám · Xộ mặt · Xẻ thô | TS · `stone_type`, `surface` |
| UAT-BID03-34 | Một vòng đã trình duyệt xong | Xuất bản báo giá định dạng PDF | Tên tệp `BG_<mã gói>_v<vòng>_<yyyymmdd>.pdf`; nội dung đủ khối định danh, bảng tổng hợp theo bộ cột đang bật, dòng TỔNG GIÁ TRỊ ĐƠN HÀNG, điều kiện thương mại và khối ký; chân trang có mã gói · số vòng · ngày giờ xuất · người xuất · số trang; tệp lưu thành chứng từ của gói ở PF-05 | XB · KQ-8 · CN-5 |
| UAT-BID03-35 | Người bấm xuất là Ban lãnh đạo | Xuất bản báo giá rồi mở tệp | Trong tệp không có giá vốn, cấu phần giá vốn, biên lợi nhuận, ngưỡng biên hay ghi chú nội bộ — không phụ thuộc người bấm xuất là ai | BR-10 · XB |
| UAT-BID03-36 | Bản nháp một vòng chưa trình duyệt xong | Xuất bản báo giá | Cảnh báo "Vòng {n} chưa duyệt xong…"; tệp mang dấu chìm "BẢN NHÁP — CHƯA TRÌNH DUYỆT" trên mọi trang, tên tệp thêm hậu tố `-nhap`, không lưu thành chứng từ | VAL-10 · XB |
| UAT-BID03-37 | Còn dòng chưa có đơn giá chào | Xuất bản báo giá | "Còn {n} dòng chưa có đơn giá chào — các dòng đó in ra dấu gạch và không cộng vào tổng. Vẫn xuất?" | VAL-9 |
| UAT-BID03-38 | Một vòng đã gửi và đã xuất tệp, sau đó mẫu in của công ty được sửa | Xuất lại vòng đó | Trả về đúng tệp đã lưu ở PF-05, không dựng lại theo mẫu mới | BR-11 |
| UAT-BID03-39 | Một vòng đã trình duyệt xong | Xuất Excel rồi mở tệp | Ô số là số thật chứ không phải chuỗi đã định dạng; dòng tổng là công thức cộng | XB |
| UAT-BID03-40 | Đã xuất PDF và Excel của một vòng | Mở dòng thời gian thương thảo | Nhật ký ghi ai xuất, lúc nào, vòng nào, định dạng gì | XB |
| UAT-BID03-41 | Vòng đã gửi, đối tác đã trả lời | Bấm **Chốt giá** nhưng bỏ trống ô phản hồi | "Ghi lại phản hồi của đối tác trước khi kết thúc vòng {n} — đây là căn cứ của quyết định này." Cả ba cách kết thúc đều đòi ghi phản hồi trước | VAL-11 · BR-12 |
| UAT-BID03-42 | Một vòng còn ở trạng thái bản nháp | Bấm **Đàm phán tiếp** | "Chỉ kết thúc được vòng đã gửi đối tác. Vòng {n} đang ở trạng thái {trạng thái}." | VAL-12 |
| UAT-BID03-43 | Tiếp UAT-BID03-41, đã ghi phản hồi | Bấm **Chốt giá** | Toàn bộ bảng áp giá đóng băng và trở thành BOQ hợp đồng ở BID-05; biên lợi nhuận tại thời điểm chốt lưu làm biên dự thầu để đối chiếu ở RPT-03; vòng đó mang nhãn "Giá chốt" | BR-13 · KQ-11 |
| UAT-BID03-44 | Vòng đã gửi, đối tác không chọn công ty | Ghi phản hồi rồi bấm **Trượt thầu** | Gói chuyển sang BID-04 | CN-4 · KQ-10 |
| UAT-BID03-45 | Vòng đã gửi, đối tác đề nghị đàm phán tiếp | Ghi phản hồi rồi bấm **Đàm phán tiếp** | Mở vòng kế tiếp và giữ nguyên giá vốn đã dựng | CN-4 · KQ-10 |
| UAT-BID03-46 | Hôm nay đã quá ngày gửi cộng hiệu lực báo giá | Mở màn hình để ký hợp đồng | "Báo giá vòng {n} đã hết hiệu lực từ {ngày}. Gửi vòng mới trước khi ký hợp đồng." | VAL-13 |
| UAT-BID03-47 | Điều kiện thương mại đã chốt ở vòng chốt giá | Mở hợp đồng ở BID-05 rồi sửa tiến độ thanh toán | Hợp đồng nạp sẵn điều kiện đã chốt; sửa thì hệ thống ghi nhận sai khác so với báo giá đã chốt và hiện cảnh báo cho người ký | BR-09 · TS · `terms` |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-2 · BR-02 → UAT-5/-16/-20/-30 · BR-03 → UAT-20/-21/-22/-23/-25 · BR-04 → UAT-26 · BR-05 → UAT-3/-15/-16 · BR-06 → UAT-17/-18 · BR-07 → UAT-19 · BR-08 → UAT-9/-41 · BR-09 → UAT-47 · BR-10 → UAT-35 · BR-11 → UAT-38 · BR-12 → UAT-7/-8/-41 · BR-13 → UAT-43. Đủ 13/13.
- **Kiểm tra dữ liệu & thông điệp**: VAL-1 → UAT-27 · VAL-2 → UAT-17 · VAL-3 → UAT-28 · VAL-4 → UAT-29 · VAL-5 → UAT-21 · VAL-6 → UAT-22 · VAL-7 → UAT-23 · VAL-8 → UAT-24 · VAL-9 → UAT-37 · VAL-10 → UAT-36 · VAL-11 → UAT-41 · VAL-12 → UAT-42 · VAL-13 → UAT-46. Đủ 13/13.
- **Kết quả mong đợi**: KQ-1 → UAT-1 · KQ-2 → UAT-20 · KQ-3 → UAT-15 · KQ-4 → UAT-19 · KQ-5 → UAT-17 · KQ-6 → UAT-9 · KQ-7 → UAT-10 · KQ-8 → UAT-34 · KQ-9 → UAT-7 · KQ-10 → UAT-7/-44/-45 · KQ-11 → UAT-43. Đủ 11/11.
- Hai mươi mốt kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này (-1 → UAT-15, -2 → UAT-17, -3 → UAT-18, -4 → UAT-19, -5 → UAT-16, -6 → UAT-43, -7 → UAT-14, -8 → UAT-10, -9 → UAT-12, -10 → UAT-20, -11 → UAT-21, -12 → UAT-26, -13 → UAT-34, -14 → UAT-35, -15 → UAT-36, -16 → UAT-38, -17 → UAT-39, -18 → UAT-7, -19 → UAT-41/-43, -20 → UAT-9, -21 → UAT-8).
- **Bảng tổng hợp giá trị đơn hàng**: nguồn của 14 cột đối chiếu theo bảng trong đặc tả, kiểm ở UAT-10, -11, -12.
- Liên màn hình: **BID-01** (mã liên kết thực xuất, thuộc tính đá), **BID-02** (chi phí biện pháp), **BID-04** (trượt thầu), **BID-05** (chốt giá, điều kiện thương mại), **PF-03** (duyệt 2 cấp), **PF-05** (tệp báo giá), **PRJ-01** (giá khoán tham chiếu), **RPT-03** (biên dự thầu).
