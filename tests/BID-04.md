# BID-04 · Huỷ gói thầu & thông báo các bộ phận — Kịch bản kiểm thử

> Nguồn: `spec/BID-04.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.
>
> Đặc tả ghi rõ **phạm vi cố ý giữ hẹp**: không có bước phê duyệt, không ghi nhận giá đối thủ, chi phí theo đuổi hay tỷ lệ trúng thầu. Bộ kịch bản này không kiểm những thứ đó.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID04-1 | Gói thầu đang đàm phán ở BID-03 | Mở màn hình huỷ gói thầu | Màn hình có ô lý do huỷ, danh sách nơi nhận thông báo và bản xem trước nội dung thông báo; không có bước phê duyệt nào | Mockup UI · CN-1…3 |
| UAT-BID04-2 | Ô lý do huỷ còn trống | Bấm **Xác nhận huỷ** | Bị chặn — ô lý do là ô nhập tự do nhưng bắt buộc | TS · `cancel_note` · CN-1 |
| UAT-BID04-3 | Đã nhập lý do huỷ | Xem bản xem trước thông báo | Nội dung ô lý do hiển thị **nguyên văn** trong thông báo, kèm mã gói thầu, tên công trình, người huỷ và thời điểm | BR-02 · CN-1 |
| UAT-BID04-4 | Đang ở khối nơi nhận thông báo | Xem danh sách mặc định | Có sẵn Ban lãnh đạo, trưởng các bộ phận và những người đã được gán vào gói thầu; cho thêm người nhận trước khi gửi | TS · `notify_to[]` · CN-3 |
| UAT-BID04-5 | Gói thầu đã huỷ | Mở lại hồ sơ gói thầu | Hồ sơ tra cứu được đầy đủ, hiện người huỷ và thời điểm (ghi tự động theo người đang đăng nhập); có thao tác mở lại gói thầu và sao chép sang gói mới | TS · `cancelled_by`, `cancelled_at` · CN (2 dòng cuối) |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID04-6 | Gói thầu đang đàm phán | Nhập lý do và xác nhận huỷ | Một thao tác đổi trạng thái gói thầu sang "Đã huỷ", đóng băng tài liệu và gửi thông báo; gói biến khỏi hàng chờ việc của mọi bộ phận | CN-2 · KQ-1 |
| UAT-BID04-7 | Tiếp UAT-BID04-6 | Kiểm tra thông báo của TP TQT và Ban dự án | Mỗi bộ phận nhận đúng **một** thông báo, đọc là biết gói thầu nào, dự án nào, lý do gì; gửi một lần, không nhắc lại | BR-03 · KQ-2 |
| UAT-BID04-8 | Gói thầu đã huỷ | Mở BOQ và bảng áp giá của gói | Còn nguyên và tra cứu được đầy đủ, đóng băng theo PF-05 — huỷ là trạng thái, không phải xoá | BR-01 · KQ-3 |
| UAT-BID04-9 | Có cơ hội tương tự gói đã huỷ | Sao chép sang gói thầu mới | Nhân bản BOQ và bảng áp giá sang gói thầu mới | KQ-3 · CN (dòng cuối) |
| UAT-BID04-10 | Huỷ nhầm gói thầu | Bấm **Mở lại gói thầu** | Gói về đúng trạng thái trước đó; nhật ký ghi người mở và thời điểm; hệ thống **không** tự gửi lại thông báo cũ | BR-04 · KQ-4 |
| UAT-BID04-11 | Gói `KH_0113` vừa bị huỷ | Tạo gói thầu mới ở cùng địa điểm | Mã mới là `KH_0114` — gói bị huỷ vẫn giữ chỗ của nó, mã đã cấp không bị cấp lại | BID-01 · BR-02 |
| UAT-BID04-12 | Người dùng không thuộc Ban lãnh đạo, không phải trưởng bộ phận, không thuộc dự án liên quan | Mở hồ sơ gói thầu đã huỷ | Không truy cập được | Quyền truy cập (PL-B) |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-8 · BR-02 → UAT-3 · BR-03 → UAT-7 · BR-04 → UAT-10. Đủ 4/4.
- **Kết quả mong đợi**: KQ-1 → UAT-6 · KQ-2 → UAT-7 · KQ-3 → UAT-8/-9 · KQ-4 → UAT-10. Đủ 4/4.
- Bốn kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1 → UAT-6 · -2 → UAT-7 · -3 → UAT-8 · -4 → UAT-10.
- Đặc tả **không có** bảng *Kiểm tra dữ liệu & thông điệp*; ràng buộc dữ liệu duy nhất là ô lý do bắt buộc (UAT-2).
- Không kiểm: giá đối thủ, chi phí theo đuổi, tỷ lệ trúng thầu — đặc tả cố ý để ngoài phạm vi GĐ1.
- Liên màn hình: **BID-01** (mã đã cấp giữ chỗ), **BID-03** (nút Trượt thầu dẫn tới đây), **PF-04** (thông báo), **PF-05** (tài liệu đóng băng).
