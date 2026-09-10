# BID-01 · Tiếp nhận thư chào thầu — Kịch bản kiểm thử

> Nguồn: `spec/BID-01.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID01-1 | Đăng nhập vai trò Phòng Đấu thầu | Mở màn hình hồ sơ gói thầu | Màn hình có đủ ba khối của mockup: thông tin gói thầu, bảng đầu việc BOQ, tệp đính kèm; nút **Chuyển thẩm định** ở đầu trang | Mockup UI · CN-1…4 |
| UAT-BID01-2 | Form gói thầu còn trống | Bấm **Lưu** | Bị chặn ở các trường ✱: tên gói thầu, bên mời thầu, địa điểm công trình, ngày nhận thư, hạn nộp hồ sơ, người phụ trách | TS (cột ✱) |
| UAT-BID01-3 | Chưa lưu lần nào | Xem ô **Mã gói thầu**, lưu lần đầu, rồi thử sửa mã | Mã tự sinh khi lưu lần đầu theo dạng `<ĐĐ>_NNNN`; sau khi sinh thì không sửa được | TS · `tender_id` · BR-01 |
| UAT-BID01-4 | Đang khai địa điểm công trình | Nhập địa điểm thuộc miền Nam | Mã thống kê vùng miền suy ra từ địa điểm và không được bỏ trống | BR-06 · TS · `site_address` |
| UAT-BID01-5 | Bên mời thầu chưa có trong danh mục đối tác | Gõ tên đối tác mới vào ô **Bên mời thầu** | Hiện "Chưa có đối tác này. Tạo nhanh với tên và mã số thuế, bổ sung thông tin sau." và cho tạo nhanh ngay tại chỗ | VAL-5 · TS · `partner_id` |
| UAT-BID01-6 | Đang ở bảng đầu việc BOQ | Mở trình quản lý cột của bảng | Khai được cột tự thêm (tên cột + kiểu chữ · số · có/không) và ẩn được cột không bắt buộc: mã HM, mã nội bộ, quy cách, ghi chú và sáu cột thuộc tính đá | TS · `boq_extra_columns[]`, `boq_hidden_columns[]` |
| UAT-BID01-7 | Có tệp Excel BOQ mời thầu | Bấm nhập từ Excel | Có bước đối chiếu cột trước khi nhận dữ liệu vào bảng | CN-2 |
| UAT-BID01-8 | Bảng BOQ đã có dòng | Sửa trực tiếp một ô trên lưới, thêm một dòng, xoá một dòng | Cả ba thao tác làm ngay trên lưới; dải tổng khối lượng theo nhóm ở đầu bảng tính lại tự động | CN-2 · KQ-3 |
| UAT-BID01-9 | Đang ở khối đính kèm | Tải lên một tệp loại "BOQ mời thầu" | Tệp vào khối đính kèm chuẩn của PF-05, có phiên bản | CN-3 · TS · `files[]` |
| UAT-BID01-10 | Gói thầu chưa đính kèm tệp BOQ nào | Bấm **Chuyển thẩm định** | Bị chặn: "Cần đính kèm BOQ mời thầu trước khi chuyển thẩm định." | VAL-1 · CN-4 · KQ-5 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID01-11 | Gói gần nhất ở Khánh Hoà là `KH_0112`; Hưng Yên đang ở `HY_0021` | Lưu gói thầu mới ở Khánh Hoà, rồi một gói ở Hưng Yên | Lần lượt nhận `KH_0113` và `HY_0022` — số thứ tự 4 chữ số chạy riêng cho từng địa điểm | BR-02 |
| UAT-BID01-12 | `KH_0113` đã bị huỷ ở BID-04 | Lưu gói thầu mới ở Khánh Hoà | Nhận `KH_0114` — lấy số lớn nhất đang dùng cộng một, không đếm số bản ghi; gói bị huỷ vẫn giữ chỗ và mã đã cấp không cấp lại | BR-02 |
| UAT-BID01-13 | Một công trình ở Quảng Ninh; một công trình không xác định được địa điểm | Lưu hai gói thầu | Tiền tố lần lượt là `MC` (Móng Cái, theo sổ cũ) và `NL` | BR-02 |
| UAT-BID01-14 | Gói `KH_0113` đã lưu | Đổi địa chỉ công trình rồi lưu lại | Mã giữ nguyên — đổi địa chỉ sau khi đã lưu không sinh lại mã | BR-01 |
| UAT-BID01-15 | Gói thầu đã thắng và khởi tạo dự án ở BID-05 | Mở hồ sơ dự án | Dự án giữ tham chiếu ngược về mã gói thầu để truy vết BOQ gốc | BR-01 |
| UAT-BID01-16 | Tệp Excel BOQ của bên mời thầu | Nhập bảng đầu việc từ tệp | Giữ nguyên thứ tự dòng và mã hạng mục gốc của bên mời thầu | BR-07 · KQ-2 |
| UAT-BID01-17 | Tiếp UAT-BID01-16 | Thêm một số đầu việc do công ty bổ sung | Dòng bổ sung được đánh dấu riêng và xếp cuối nhóm tương ứng | BR-07 |
| UAT-BID01-18 | Bảng đầu việc đã nhập từ tệp | Mở một dòng, rồi xuất một văn bản gửi bên mời thầu và một báo cáo nội bộ | Dòng lưu song song mã hạng mục của bên mời thầu và mã nội bộ; văn bản gửi ra ngoài dùng mã bên mời thầu, báo cáo nội bộ dùng mã nội bộ | BR-04 |
| UAT-BID01-19 | Bảng BOQ đã nhập đủ | Xem đầu bảng | Tổng khối lượng theo nhóm đầu việc tính tự động và hiển thị ở đầu bảng | KQ-3 |
| UAT-BID01-20 | Chỉ đính kèm BOQ bản scan, bảng đầu việc chưa số hoá | Bấm **Chuyển thẩm định**, rồi mở BID-02 | Cho chuyển nhưng gắn nhãn "cần số hoá"; bước thẩm định chỉ cho nhận xét chung, không đối chiếu theo dòng | BR-03 |
| UAT-BID01-21 | Hạn nộp còn 3 ngày | Chờ tới mốc nhắc, rồi tới mốc còn 1 ngày | Người phụ trách nhận thông báo ở cả hai mốc | BR-05 · KQ-4 · TS · `due_at` |
| UAT-BID01-22 | Quá hạn nộp mà gói chưa ở trạng thái đã gửi báo giá | Mở danh sách gói thầu | Gói gắn cờ đỏ trên danh sách và trưởng phòng nhận thông báo | BR-05 |
| UAT-BID01-23 | Đang khai hạn nộp hồ sơ | Chọn ngày ở quá khứ | "Hạn nộp đã qua. Xác nhận nếu đây là gói thầu nhập bổ sung cho hồ sơ cũ." | VAL-2 |
| UAT-BID01-24 | Một dòng của bảng đầu việc | Nhập khối lượng `0`, rồi `-5` | "Khối lượng dòng {stt} phải lớn hơn 0. Nếu chưa có số liệu, để trống và ghi chú 'chờ làm rõ'." — thông điệp nêu đúng số thứ tự dòng | VAL-3 |
| UAT-BID01-25 | Hai dòng cùng mang một mã hạng mục của bên mời thầu | Lưu bảng | "Mã hạng mục {mã} xuất hiện ở dòng {a} và {b}. Kiểm tra lại BOQ gốc trước khi nhập." — nêu đủ cả hai số dòng | VAL-4 |
| UAT-BID01-26 | Bảng đã có 12 cột tự thêm | Thêm cột thứ 13 | "Bảng chỉ thêm được tối đa 12 cột." | VAL-6 · TS · `boq_extra_columns[]` |
| UAT-BID01-27 | Trình quản lý cột đang mở | Lần lượt thử ẩn **Đầu việc**, **ĐVT**, **Khối lượng** | Cả ba lần chặn: "Cột {tên} là cột bắt buộc, không ẩn được." Ba cột này luôn hiển thị vì mọi quy tắc phía sau đọc chúng | VAL-7 · BR-09 |
| UAT-BID01-28 | Bảng đã có cột hệ thống tương ứng | Thêm cột tự thêm mà tên sinh ra mã trùng cột có sẵn | "Mã cột {mã} trùng với một cột có sẵn của bảng." | VAL-8 |
| UAT-BID01-29 | Hồ sơ mời thầu có cột "Chiều rộng" mà hệ thống chưa có | Thêm cột "Chiều rộng" kiểu số, ẩn "Dày" và "Sai số", nhập một dòng rồi lưu và mở lại | Bảng nhận đúng bộ cột của hồ sơ này; mở lại vẫn giữ nguyên cột và giá trị đã nhập | KQ-6 · BR-08 |
| UAT-BID01-30 | Bảng đang có cột tự thêm đã nhập dữ liệu | Xoá cột đó và lưu cấu trúc bảng | Có cảnh báo trước khi lưu; sau khi lưu, mọi giá trị đã nhập ở cột đó bị xoá cùng lúc | BR-10 |
| UAT-BID01-31 | Tiếp UAT-BID01-30 | Thêm lại cột cùng tên | Ra một cột trống, không khôi phục số liệu cũ | BR-10 |
| UAT-BID01-32 | Gói thầu đã khai bộ cột riêng, đã qua thẩm định | Mở BOQ cập nhật ở BID-02 và BOQ hợp đồng ở BID-05 | Cả hai giữ nguyên bộ cột đã chốt, không đọc lại bộ cột của bản nháp | BR-08 |
| UAT-BID01-33 | Gói thầu đã khai bộ cột riêng | Sao chép gói thầu | Gói mới chép luôn bộ cột | BR-08 |
| UAT-BID01-34 | Gói thầu đã khai bộ cột riêng | Dán vùng chọn Excel có đủ cột hệ thống và các cột tự thêm ở cuối; sau đó dán một bản 5 cột kiểu cũ | Cột tự thêm nhận đúng giá trị theo thứ tự đã khai; bản dán 5 cột kiểu cũ vẫn nhập được như trước | Kịch bản kiểm thử của đặc tả · UAT-BID01-8 |
| UAT-BID01-35 | Gói thầu khai đủ và đã lưu | Mở danh sách gói thầu | Gói có mã duy nhất và xuất hiện trong danh sách với trạng thái "Nháp"; chỉ **Chuyển thẩm định** mới đưa sang "Đang thẩm định" | KQ-1 |
| UAT-BID01-36 | Bảng đầu việc có 3 dòng đã nhập đủ | Xoá ô **ĐVT** của dòng 2 rồi bấm **Lưu bảng đầu việc** | Nút lưu khoá chừng nào ô còn trống và ô thiếu được đánh dấu trên lưới; cả bảng không lưu, dòng 2 vẫn còn nguyên — hệ thống không tự bỏ dòng thiếu ô để lưu phần còn lại | BR-11 |
| UAT-BID01-37 | Tiếp UAT-BID01-36 | Điền lại ĐVT dòng 2; để trống khối lượng dòng 3 kèm ghi chú "chờ làm rõ" rồi lưu | Lưu thành công đủ 3 dòng — khối lượng để trống là hợp lệ; chỉ khối lượng đã điền mới phải lớn hơn 0 | BR-11 |
| UAT-BID01-38 | Bảng đầu việc 5 dòng; dòng 2 và dòng 5 có ô **Quy cách** dài 2.500 ký tự, mọi ô bắt buộc đều đã điền nên nút lưu không bị khoá | Bấm **Lưu bảng đầu việc** | Máy chủ chặn dù lưới đã cho lưu: "Chưa lưu được bảng đầu việc: 2 dòng chưa hợp lệ, dòng đầu tiên là dòng 2 — cột Quy cách. Cả 2 dòng đều được đánh dấu trên lưới; sửa hết rồi lưu lại. Bảng vẫn giữ nguyên như trước khi lưu." Bảng vẫn còn đủ 5 dòng như trước khi lưu | VAL-9 |
| UAT-BID01-39 | Bảng đầu việc đang mở | Nhập ô **Đầu việc** 1.000 ký tự và ô **Quy cách** 2.000 ký tự rồi lưu | Lưu thành công — giới hạn là 1.000 ký tự cho đầu việc và 2.000 ký tự cho quy cách | TS · `boq_lines[]` |
| UAT-BID01-40 | Gói thầu đang dùng cột có sẵn **Quy cách**; trình quản lý cột đang mở | Thêm một cột tự thêm đặt tên "Quy cách"; thử lại với " quy cách " | Cả hai lần đều bị chặn ngay lúc thêm cột: "Bảng đã có cột tên 'Quy cách'. Đặt tên khác để hai cột không trùng tiêu đề." — so trên tên hiển thị, không phân biệt hoa thường và bỏ khoảng trắng thừa | VAL-10 |
| UAT-BID01-41 | Gói thầu có từ trước khi có quy tắc này, đang mang cột có sẵn **Quy cách** và một cột tự thêm cũng tên "Quy cách" — đúng dữ liệu của BUG-BID-022 | Mở trình quản lý cột, đổi tên một cột khác rồi bấm lưu cấu trúc bảng | Lưu bị chặn: "Bảng đã có cột tên 'Quy cách'. Đặt tên khác để hai cột không trùng tiêu đề." — chốt chặn chạy cả lúc lưu cấu trúc bảng chứ không chỉ lúc thêm cột, nên gói mang sẵn hai cột trùng tiêu đề cũng phải sửa mới lưu được | VAL-10 |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-3/-14/-15 · BR-02 → UAT-11/-12/-13 · BR-03 → UAT-10/-20 · BR-04 → UAT-18 · BR-05 → UAT-21/-22 · BR-06 → UAT-4 · BR-07 → UAT-16/-17 · BR-08 → UAT-29/-32/-33 · BR-09 → UAT-27 · BR-10 → UAT-30/-31 · BR-11 → UAT-36/-37. Đủ 11/11.
- **Kiểm tra dữ liệu & thông điệp**: VAL-1 → UAT-10 · VAL-2 → UAT-23 · VAL-3 → UAT-24 · VAL-4 → UAT-25 · VAL-5 → UAT-5 · VAL-6 → UAT-26 · VAL-7 → UAT-27 · VAL-8 → UAT-28 · VAL-9 → UAT-38 · VAL-10 → UAT-40/-41. Đủ 10/10.
- **Kết quả mong đợi**: KQ-1 → UAT-35 · KQ-2 → UAT-16 · KQ-3 → UAT-8/-19 · KQ-4 → UAT-21 · KQ-5 → UAT-10 · KQ-6 → UAT-29. Đủ 6/6.
- Tám kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: UAT-BID01-1 → UAT-16/-19 · -2 → UAT-10 · -3 → UAT-21 · -4 → UAT-20 · -5 → UAT-4 · -6 → UAT-29 · -7 → UAT-30/-31 · -8 → UAT-34.
- **Khoảng trống của đặc tả — đã đóng.** Ba điểm từng chặn việc dựng kịch bản (đặc tả nói Đầu việc · ĐVT · Khối lượng là cột **không ẩn được** ở BR-09 nhưng không nói ô nào bắt buộc khi lưu; không nói giới hạn độ dài của ô *Quy cách* và ô *Đầu việc*; không có thông điệp cho trường hợp máy chủ từ chối cả bảng) đã được bổ sung vào `spec/BID-01.html`: **BR-11** chốt Đầu việc và ĐVT là hai ô bắt buộc — khối lượng được để trống kèm ghi chú "chờ làm rõ", đã điền thì phải lớn hơn 0 theo VAL-3; **TS · `boq_lines[]`** ghi giới hạn 1.000 ký tự cho đầu việc và 2.000 ký tự cho quy cách; **VAL-9** quy định thông điệp khi máy chủ từ chối cả bảng. Kịch bản tương ứng: UAT-36…-39. Nguồn gốc: issue "[BID-01] Lưu bảng đầu việc BOQ…".
- Liên màn hình: **BID-02** (bộ cột ở chế độ xem đầy đủ, nhãn cần số hoá), **BID-04** (mã đã cấp giữ chỗ), **BID-05** (BOQ hợp đồng giữ bộ cột, tham chiếu ngược), **PF-01** (danh mục đối tác), **PF-04** (nhắc hạn), **PF-05** (đính kèm và phiên bản).
