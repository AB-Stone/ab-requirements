# BID-04 · Huỷ gói thầu & thông báo các bộ phận — Kịch bản kiểm thử

> Nguồn: `spec/BID-04.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.
>
> Đặc tả ghi rõ **phạm vi cố ý giữ hẹp**: không có bước phê duyệt, không ghi nhận giá đối thủ, chi phí theo đuổi hay tỷ lệ trúng thầu. Bộ kịch bản này không kiểm những thứ đó.
>
> Kịch bản bổ sung theo YC-04 đánh số tiếp từ 13; số cũ (1–12) giữ nguyên để còn đối chiếu được với đợt chạy `KQ-UAT-BID-20260903.md`, nên nhóm A không liền số.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID04-1 | Gói thầu đang đàm phán ở BID-03 | Mở màn hình huỷ gói thầu | Màn hình có ô lý do huỷ, danh sách nơi nhận thông báo và bản xem trước nội dung thông báo; không có bước phê duyệt nào | Mockup UI · CN-1…3 |
| UAT-BID04-2 | Ô lý do huỷ còn trống | Bấm **Xác nhận huỷ** | Bị chặn — ô lý do là ô nhập tự do nhưng bắt buộc | TS · `cancel_note` · CN-1 |
| UAT-BID04-3 | Đã nhập lý do huỷ | Xem bản xem trước thông báo | Nội dung ô lý do hiển thị **nguyên văn** trong thông báo, kèm mã gói thầu, tên công trình, người huỷ và thời điểm | BR-02 · CN-1 |
| UAT-BID04-4 | Đang ở khối nơi nhận thông báo | Xem danh sách mặc định | Có sẵn Ban lãnh đạo, trưởng các bộ phận và những người đã được gán vào gói thầu; cho thêm người nhận trước khi gửi | TS · `notify_to[]` · CN-3 |
| UAT-BID04-5 | Gói thầu đã huỷ | Mở lại hồ sơ gói thầu | Hồ sơ tra cứu được đầy đủ, hiện người huỷ và thời điểm (ghi tự động theo người đang đăng nhập); có thao tác mở lại gói thầu và sao chép sang gói mới | TS · `cancelled_by`, `cancelled_at` · CN (2 dòng cuối) |
| UAT-BID04-13 | Gói `KH_0001` đã được gộp vào `KH_0004` ở BID-01, đang mang trạng thái *Đã gộp* | Mở hồ sơ `KH_0001`, tìm nút **Huỷ gói thầu** | Nút không hiện — chặn ngay ở thiết kế, không phải bấm rồi mới báo lỗi; hồ sơ nêu gói đã gộp vào `KH_0004` kèm liên kết sang gói đích | BR-06 |
| UAT-BID04-14 | Gói đã sang *Đã ký HĐ* và dự án đã khởi tạo ở BID-05 | Mở hồ sơ gói, tìm nút **Huỷ gói thầu** | Nút không hiện — dừng một hợp đồng đã ký là việc thanh lý hợp đồng, không làm ở màn hình này | BR-05 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID04-6 | Gói thầu đang đàm phán | Nhập lý do và xác nhận huỷ | Một thao tác đổi trạng thái gói thầu sang "Đã huỷ", đóng băng tài liệu và gửi thông báo; gói biến khỏi hàng chờ việc của mọi bộ phận | CN-2 · KQ-1 |
| UAT-BID04-7 | Tiếp UAT-BID04-6 | Kiểm tra thông báo của TP TQT và Ban dự án | Mỗi bộ phận nhận đúng **một** thông báo, đọc là biết gói thầu nào, dự án nào, lý do gì; gửi một lần, không nhắc lại | BR-03 · KQ-2 |
| UAT-BID04-8 | Gói thầu đã huỷ | Mở BOQ và bảng áp giá của gói | Còn nguyên và tra cứu được đầy đủ, đóng băng theo PF-05 — huỷ là trạng thái, không phải xoá | BR-01 · KQ-3 |
| UAT-BID04-9 | Có cơ hội tương tự gói đã huỷ | Sao chép sang gói thầu mới | Nhân bản BOQ và bảng áp giá sang gói thầu mới | KQ-3 · CN (dòng cuối) |
| UAT-BID04-10 | Huỷ nhầm gói thầu | Bấm **Mở lại gói thầu** | Gói về đúng trạng thái trước đó; nhật ký ghi người mở và thời điểm; hệ thống **không** tự gửi lại thông báo cũ | BR-04 · KQ-4 |
| UAT-BID04-11 | Gói `KH_0113` vừa bị huỷ | Tạo gói thầu mới ở cùng địa điểm | Gợi ý mã cho gói mới là `KH_0114` — gói bị huỷ vẫn giữ chỗ của nó, mã đã cấp không bị cấp lại; ô mã tự nhập nên đây là gợi ý, sửa được | BID-01 · BR-03 |
| UAT-BID04-12 | Người dùng không thuộc Ban lãnh đạo, không phải trưởng bộ phận, không thuộc dự án liên quan | Mở hồ sơ gói thầu đã huỷ | Không truy cập được | Quyền truy cập (PL-B) |
| UAT-BID04-15 | Tiếp UAT-BID04-13, gói đích `KH_0004` đang thẩm định | Nhập lý do và huỷ `KH_0004` | Huỷ được; `KH_0004` chuyển *Đã huỷ*. Các gói nguồn giữ nguyên *Đã gộp* và vẫn trỏ về `KH_0004` — không tự chuyển sang *Đã huỷ* theo | BR-06 |
| UAT-BID04-16 | `KH_0001` và `KH_0002` đã gộp vào `KH_0004`; gói đích còn ở *Nháp* | Bấm **Bỏ gộp** ở `KH_0004`, rồi mở `KH_0001` và huỷ | Hai gói nguồn về đúng trạng thái trước khi gộp; nút **Huỷ gói thầu** hiện lại và huỷ được như bình thường | BR-06 · BID-01 |
| UAT-BID04-17 | Gói đích `KH_0004` đã chuyển thẩm định | Tìm thao tác **Bỏ gộp** ở `KH_0004` | Không còn — sau khi gói đích rời *Nháp* thì đường duy nhất là huỷ chính gói đích ở màn hình này; gói nguồn ở lại *Đã gộp* | BR-06 · BID-01 |
| UAT-BID04-18 | Tiếp UAT-BID04-15 | Kiểm tra thông báo đã gửi | Thông báo nêu `KH_0004`; không có thông báo "đã huỷ" nào phát cho `KH_0001`, `KH_0002` — trạng thái hiển thị và thông báo đã phát cùng kể một câu chuyện | BR-06 · BR-03 |

## Ghi chú bao phủ

- **Quy tắc nghiệp vụ**: BR-01 → UAT-8 · BR-02 → UAT-3 · BR-03 → UAT-7/-18 · BR-04 → UAT-10 · BR-05 → UAT-14 · BR-06 → UAT-13/-15/-16/-17/-18. Đủ 6/6.
- **Kết quả mong đợi**: KQ-1 → UAT-6 · KQ-2 → UAT-7 · KQ-3 → UAT-8/-9 · KQ-4 → UAT-10. Đủ 4/4.
- Sáu kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1 → UAT-6 · -2 → UAT-7 · -3 → UAT-8 · -4 → UAT-10 · -5 → UAT-13 · -6 → UAT-15.
- Đặc tả **không có** bảng *Kiểm tra dữ liệu & thông điệp*; ràng buộc dữ liệu duy nhất là ô lý do bắt buộc (UAT-2).
- Hai trạng thái bị chặn huỷ ở màn hình này đều **chặn bằng cách ẩn nút**, không bằng thông điệp lỗi — UAT-13 và UAT-14 kiểm sự vắng mặt của nút, không kiểm nội dung thông báo lỗi.
- **Khoảng trống của đặc tả**: ranh giới giữa huỷ gói thầu và thanh lý hợp đồng đã ký chưa chốt (PL-C · Q-27) nên không có kịch bản nào cho đường thanh lý. Đặc tả cũng không nói gói *Đã gộp* có mở lại được không sau khi gói đích bị huỷ — chưa đặt kịch bản.
- Không kiểm: giá đối thủ, chi phí theo đuổi, tỷ lệ trúng thầu — đặc tả cố ý để ngoài phạm vi GĐ1.
- Liên màn hình: **BID-01** (mã đã cấp giữ chỗ, gộp gói & Bỏ gộp), **BID-03** (nút Trượt thầu dẫn tới đây), **BID-05** (gói đã ký HĐ), **PF-04** (thông báo), **PF-05** (tài liệu đóng băng), **PL-A** (*Đã gộp* ≠ *Đã huỷ*).
