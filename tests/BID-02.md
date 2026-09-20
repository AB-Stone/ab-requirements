# BID-02 · Thẩm định BOQ liên phòng ban — Kịch bản kiểm thử

> Nguồn: `spec/BID-02.html`. Mọi kịch bản dưới đây suy ra từ một mục cụ thể của đặc tả, ghi ở cột **Nguồn**:
> `TS` = bảng *Tham số* (phiếu rà soát, kiểm tra Hồ sơ mời thầu / phiếu khảo sát) · `CN-n` = dòng *Chức năng trên màn hình* · `BR-nn` = *Quy tắc nghiệp vụ* · `VAL-n` = dòng *Kiểm tra dữ liệu & thông điệp* · `KQ-n` = gạch đầu dòng *Kết quả mong đợi*.

## A · Luồng giao diện

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID02-1 | Gói thầu vừa chuyển thẩm định ở BID-01 | Mở hàng chờ việc của P. TQT và của Ban dự án | Hai phiếu hiển thị cùng lúc trên hàng chờ của hai bộ phận, có cùng hạn xử lý: P. TQT thấy dòng **Rà soát, kiểm tra Hồ sơ mời thầu**, Ban dự án thấy dòng **Khảo sát hiện trường** | KQ-1 · BR-01 |
| UAT-BID02-2 | Mở phiếu **Rà soát, kiểm tra Hồ sơ mời thầu** lần đầu | Xem bảng đối chiếu | Mặc định chế độ **gọn**, đúng bảy cột: mã HM, đầu việc, ĐVT, khối lượng mời thầu, kết luận, khối lượng đề xuất, ghi chú | TS · `view_mode` · BR-03 |
| UAT-BID02-3 | Đang ở một dòng BOQ | Chọn kết luận **Sai khối lượng**, rồi đổi sang kết luận khác | Chọn "sai khối lượng" thì mở ô **Khối lượng đề xuất** và ô đó thành bắt buộc; đổi kết luận khác thì ô đóng lại | CN-1 · TS · `qty_proposed` |
| UAT-BID02-4 | Phiếu **Rà soát, kiểm tra Hồ sơ mời thầu** còn dòng chưa kết luận | Nhìn nút **Nộp phiếu** | Ngay trên nút hiện bộ đếm "còn {n} dòng chưa kết luận" | BR-02 |
| UAT-BID02-5 | Gói thầu đã khai bộ cột riêng ở BID-01 — thêm "Chiều rộng", ẩn "Sai số" | Bấm **Xem đầy đủ** | Bảng dựng lại nguyên bảng đầu việc của BID-01 theo đúng bộ cột của gói (có "Chiều rộng", không có "Sai số"), khối kết luận nối vào cuối; cột lấy từ BOQ gốc chỉ đọc | CN-6 · BR-03 · KQ-2 |
| UAT-BID02-6 | Đang ở chế độ đầy đủ, có một dòng đang nhập dở | Đổi về chế độ gọn rồi quay lại đầy đủ | Phần đang nhập dở không mất qua cả hai lần đổi | BR-03 |
| UAT-BID02-7 | Người dùng A đóng phiếu khi đang ở chế độ đầy đủ | A mở lại phiếu; B mở cùng phiếu đó | A vào lại chế độ đã chọn, B vẫn ở chế độ mặc định — hệ thống nhớ lựa chọn cho lần mở sau, mỗi người một kiểu | TS · `view_mode` |
| UAT-BID02-8 | Phiếu khảo sát hiện trường đang mở | Kéo thả nhiều tệp một lần: ảnh JPG và video MP4 | Kéo thả hoặc bấm chọn ngay trên phiếu, nhiều tệp một lần; tệp vào thư viện của phiếu | TS · `photos[]` |
| UAT-BID02-9 | Bảng biện pháp thi công khó đang có nhiều dòng | Nhập chi phí ước tính cho từng dòng | Ô **Chi phí phát sinh** là tổng cộng của bảng biện pháp, cộng tự động | CN-3 · TS · `extra_cost` |
| UAT-BID02-10 | Phiếu **Rà soát, kiểm tra Hồ sơ mời thầu** đã nộp, phiếu hiện trường chưa | Xem nút **Chốt BOQ cập nhật** | Nút bị vô hiệu và ghi rõ đang thiếu phiếu nào | BR-01 · KQ-7 |
| UAT-BID02-11 | Đã chốt BOQ cập nhật | Bấm **So sánh v1 ↔ v2** | Hiện cạnh nhau v1 và v2, tô màu dòng sửa khối lượng và dòng thêm mới | CN-4 · KQ-4 |
| UAT-BID02-37 | Gói `GT-2026-018` đang ở trạng thái *Đang thẩm định* | Mở BID-02, rồi xem gói trên danh sách gói thầu | Tiêu đề màn hình vẫn **Thẩm định BOQ liên phòng ban**; tab đầu là **Rà soát, kiểm tra Hồ sơ mời thầu**, tab thứ hai vẫn **Khảo sát hiện trường**; trạng thái gói vẫn ghi *Đang thẩm định* — đổi tên phiếu không kéo theo tên màn hình, tên tab khảo sát hay tên trạng thái | Mockup UI · TS (ghi chú cuối bảng) · YC-03 |
| UAT-BID02-38 | Phiếu **Rà soát, kiểm tra Hồ sơ mời thầu** đang mở | Xem nhãn trường người lập phiếu | Nhãn hiển thị **Người rà soát**; mã trường vẫn là `reviewer`, cùng với `line_check[]`, `extra_lines[]`, `qty_proposed` — đổi nhãn không đổi mã, tham chiếu từ trang khác không hỏng | TS · `reviewer` · YC-03 |
| UAT-BID02-39 | BOQ có dòng 1 mang mã CĐT `HM-01`, dòng 2 để trống mã CĐT; phiếu đang ở chế độ gọn | Đọc cột **mã HM** của hai dòng | Dòng 1 hiện `HM-01`; dòng 2 không có mã CĐT nên rơi về mã hệ thống dạng `<mã gói>.NN` — chế độ gọn chỉ còn chỗ cho một cột mã và ưu tiên thứ tiếng mà bên mời thầu dùng | TS · `view_mode` · BR-03 |
| UAT-BID02-40 | Tiếp UAT-BID02-39 | Bấm **Xem đầy đủ** | Cả hai cột mã cùng hiện — Mã HM của hệ thống và Mã HM của CĐT; mã hệ thống mới là thứ BID-03 và hợp đồng bám vào | BR-03 · CN-6 |

## B · Chức năng & nghiệp vụ

| Mã | Tiền đề | Thao tác | Kết quả kỳ vọng | Nguồn |
|---|---|---|---|---|
| UAT-BID02-12 | Phiếu **Rà soát, kiểm tra Hồ sơ mời thầu** còn dòng chưa kết luận | Bấm **Nộp phiếu** | "Còn {n} dòng chưa có kết luận. Bấm để nhảy tới dòng đầu tiên." — bấm vào thì nhảy đúng tới dòng đó | VAL-1 · BR-02 |
| UAT-BID02-13 | Một dòng kết luận "sai khối lượng" | Nhập khối lượng đề xuất đúng bằng khối lượng mời thầu | "Khối lượng đề xuất trùng khối lượng mời thầu. Đổi kết luận sang 'đúng' hoặc sửa lại số." | VAL-2 · BR-05 |
| UAT-BID02-14 | Đang ở chế độ đầy đủ | Sửa một ô thuộc bảng đầu việc BID-01 của dòng BOQ gốc | "Cột này thuộc BOQ mời thầu, không sửa ở bước thẩm định. Ghi kết luận 'sai quy cách' kèm đề xuất để bước hợp nhất xử lý." | VAL-3 · BR-04 |
| UAT-BID02-15 | Tiếp UAT-BID02-14 | Sửa cùng ô đó trên một dòng bổ sung do phiếu thẩm định thêm vào | Sửa được bình thường — dòng bổ sung nhập đủ cột vì chưa có trong bản gốc | BR-04 |
| UAT-BID02-16 | Đang ở chế độ đầy đủ, còn dòng chưa kết luận | Đổi về chế độ gọn rồi bấm **Nộp phiếu** | Bộ đếm không đổi — luôn đếm trên toàn bộ dòng BOQ, không đếm theo số dòng đang nhìn thấy | BR-03 |
| UAT-BID02-17 | Phiếu hiện trường có một dòng biện pháp chưa gắn hạng mục | Nộp phiếu | "Chọn dòng BOQ mà biện pháp này phục vụ, để chi phí vào đúng cấu phần giá vốn." | VAL-4 · BR-06 |
| UAT-BID02-18 | Phiếu hiện trường đã ghi biện pháp khó, mới có 2 tệp đã xác nhận | Bấm **Nộp phiếu** | "Cần tối thiểu 3 ảnh hiện trường cho phần biện pháp thi công khó." Tải thêm 1 tệp rồi nộp lại thì thành công | VAL-5 · BR-07 |
| UAT-BID02-19 | Phiếu có biện pháp khó, 2 ảnh và 1 video | Bấm **Nộp phiếu** | Nộp được — một video tính như một ảnh | BR-07 · TS · `photos[]` |
| UAT-BID02-20 | Phiếu hiện trường đang mở | Thả một tệp có phần mở rộng ngoài danh sách cho phép | "Tệp {tên} không phải ảnh hoặc video — cho phép .jpg, .jpeg, .png, .webp, .heic, .heif, .mp4, .mov, .m4v, .webm." | VAL-6 |
| UAT-BID02-21 | Phiếu hiện trường đang mở | Thả một ảnh > 12 MB, rồi một video > 200 MB | "Tệp {tên} nặng {x} MB, vượt giới hạn {giới hạn} MB." — chặn ngay trên trình duyệt trước khi tải, và kiểm lại theo dung lượng thật khi xác nhận | VAL-7 |
| UAT-BID02-22 | Phiếu hiện trường đã có 40 tệp | Thả thêm một tệp | "Một phiếu khảo sát giữ tối đa 40 tệp (hiện có {n})." Tệp đang tải dở cũng tính vào con số này | VAL-8 |
| UAT-BID02-23 | Một tệp tải lên bị đứt giữa chừng | Mở thư viện của phiếu và thử xác nhận | Tệp tải dở không hiện trong thư viện và không được đếm; xác nhận khi kho chưa có đối tượng thì báo "Chưa thấy tệp {tên} trên kho lưu trữ — tải lại tệp trước khi xác nhận." | VAL-9 · TS · `photos[]` |
| UAT-BID02-24 | Phiếu hiện trường đã nộp | Mở lại phiếu, thử thêm và thử xoá tệp | Chỉ xem — "Phiếu đã nộp — mở lại phiếu nếu cần bổ sung ảnh." | VAL-10 |
| UAT-BID02-25 | Phiếu hiện trường có ảnh | Chép liên kết một ảnh rồi mở lại liên kết đó sau khi hết hạn; sau đó mở lại phiếu | Liên kết cấp cho từng tệp là liên kết có chữ ký và có hạn, hết hạn thì không mở được; mở lại phiếu thì hệ thống cấp liên kết mới | TS · `photos[]` (kho đối tượng) |
| UAT-BID02-26 | Một trong hai phiếu chưa nộp | Bấm **Chốt BOQ cập nhật** | "Chưa nhận được {tên phiếu} từ {bộ phận}. Đã gửi nhắc lúc {giờ}." | VAL-11 · BR-01 |
| UAT-BID02-27 | Cả hai phiếu đã nộp; có dòng sửa khối lượng, dòng bổ sung và dòng giữ nguyên | Chốt BOQ cập nhật rồi mở so sánh v1 ↔ v2 | BOQ v2 giữ nguyên mọi dòng gốc, đánh dấu ba loại (giữ nguyên · sửa khối lượng · bổ sung mới); so sánh hiện số dòng từng loại và chênh lệch tổng khối lượng theo nhóm | BR-08 · KQ-3 · KQ-4 |
| UAT-BID02-28 | Tiếp UAT-BID02-27 | Mở lại hai phiếu thẩm định và mở bước áp giá | Chốt sinh phiên bản BOQ mới, khoá hai phiếu thẩm định và mở bước áp giá | CN-5 |
| UAT-BID02-29 | Phiếu hiện trường ghi các biện pháp gắn về một dòng BOQ, tổng 960 triệu | Chuyển sang bước áp giá BID-03 | Chi phí biện pháp tự chuyển thành cấu phần giá vốn của **đúng dòng đã gắn**, không phải nhập lại và không phân bổ đều | BR-06 · KQ-5 |
| UAT-BID02-30 | Tổng khối lượng v2 lệch quá 10% so với BOQ mời thầu | Bấm chốt | Hệ thống nhắc lập văn bản làm rõ trước khi chốt; cho phép bỏ qua nhưng phải ghi lý do | BR-09 |
| UAT-BID02-31 | Tiếp UAT-BID02-30, đã bỏ qua kèm lý do | Mở màn hình áp giá BID-03 | Lý do hiện lại ở bước áp giá | BR-09 |
| UAT-BID02-32 | Quá hạn mà một trong hai phiếu chưa nộp | Chờ tới mốc quá hạn | Gói thầu bị gắn cờ và trưởng bộ phận tương ứng nhận nhắc việc | KQ-6 |
| UAT-BID02-33 | Gói thầu gắn nhãn "cần số hoá" ở BID-01 | Mở phiếu **Rà soát, kiểm tra Hồ sơ mời thầu** | Chỉ cho nhận xét chung, không đối chiếu theo dòng | BID-01 · BR-03 |
| UAT-BID02-34 | BOQ mời thầu 6 dòng, riêng dòng 3 để trống khối lượng kèm ghi chú "chờ làm rõ" — trạng thái hợp lệ mang sang từ BID-01 | Chọn kết luận "đúng" cho dòng 3, để trống khối lượng đề xuất, rồi bấm nộp phiếu rà soát | Bị chặn: dòng chưa có khối lượng không kết luận "đúng" được, phải điền khối lượng đề xuất thì phiếu mới nộp được — đây là bước cuối còn đổi được khối lượng | BR-10 |
| UAT-BID02-35 | Tiếp UAT-BID02-34 | Đọc thông điệp hiện trên nút nộp | "Dòng 3 chưa có khối lượng. Nhập khối lượng đề xuất trước khi nộp phiếu — đây là bước cuối còn đổi được khối lượng." — nêu đúng số thứ tự dòng còn trống | VAL-12 |
| UAT-BID02-36 | Tiếp UAT-BID02-34; dòng 3 vẫn để trống khối lượng | Đổi kết luận dòng 3 sang "trùng lặp", vẫn để trống khối lượng đề xuất, rồi bấm nộp phiếu rà soát | Vẫn bị chặn, nguyên văn thông điệp cũ: "Dòng 3 chưa có khối lượng. Nhập khối lượng đề xuất trước khi nộp phiếu — đây là bước cuối còn đổi được khối lượng." — cổng nộp không phụ thuộc kết luận đã chọn | BR-10 |
| UAT-BID02-41 | Gói gộp từ hai gói cũ ở BID-01; hai dòng đến từ hai gói nguồn khác nhau cùng mang mã CĐT `HM-01` | Mở bảng đối chiếu ở chế độ gọn và kết luận cho cả hai dòng | Hai dòng vẫn tách được: mỗi dòng thuộc một nhóm gói nguồn riêng và mã hệ thống của hai dòng khác nhau; trùng mã CĐT sau khi gộp là trạng thái hợp lệ, không phải lỗi, và không chặn việc nộp phiếu | BR-03 · BID-01 |
| UAT-BID02-42 | Phiếu rà soát chưa nộp, đã quá hạn | Đọc dòng hàng chờ việc, tiêu đề tab, nhãn nút nộp và nội dung nhắc hạn | Cả bốn chỗ gọi phiếu bằng đúng một tên **Rà soát, kiểm tra Hồ sơ mời thầu**; không còn chuỗi "phiếu kỹ thuật" hay "thẩm định kỹ thuật" ở bất kỳ chỗ nào | KQ-1 · YC-03 |
| UAT-BID02-43 | Gói thầu *Đang thẩm định*, phiếu hiện trường chưa nộp | Bấm **Chốt BOQ cập nhật**, rồi đọc trạng thái gói trên danh sách | Thông điệp chặn gọi đúng tên phiếu mới; trạng thái gói vẫn là *Đang thẩm định* (PL-A) — trạng thái thuộc về gói thầu và nói rằng đang chờ **cả hai** phiếu, nên không đổi theo tên một phiếu | VAL-11 · BR-01 · YC-03 |

## Ghi chú bao phủ

- **Đánh số.** Kịch bản bổ sung theo YC-03 và YC-04 đánh số tiếp từ 37; số cũ (1–36) giữ nguyên để còn đối chiếu được với đợt chạy `KQ-UAT-BID-20260903.md`, nên nhóm A không liền số (1–11 và 37–40). Các kịch bản chỉ đổi tên phiếu được **sửa tại chỗ**, giữ nguyên mã.
- **Quy tắc nghiệp vụ**: BR-01 → UAT-1/-10/-26/-43 · BR-02 → UAT-4/-12 · BR-03 → UAT-2/-5/-6/-16/-39/-40/-41 · BR-04 → UAT-14/-15 · BR-05 → UAT-13 · BR-06 → UAT-17/-29 · BR-07 → UAT-18/-19 · BR-08 → UAT-27 · BR-09 → UAT-30/-31 · BR-10 → UAT-34/-36. Đủ 10/10.
- **Kiểm tra dữ liệu & thông điệp**: VAL-1 → UAT-12 · VAL-2 → UAT-13 · VAL-3 → UAT-14 · VAL-4 → UAT-17 · VAL-5 → UAT-18 · VAL-6 → UAT-20 · VAL-7 → UAT-21 · VAL-8 → UAT-22 · VAL-9 → UAT-23 · VAL-10 → UAT-24 · VAL-11 → UAT-26/-43 · VAL-12 → UAT-35. Đủ 12/12.
- **Kết quả mong đợi**: KQ-1 → UAT-1/-42 · KQ-2 → UAT-5 · KQ-3 → UAT-27 · KQ-4 → UAT-11/-27 · KQ-5 → UAT-29 · KQ-6 → UAT-32 · KQ-7 → UAT-10. Đủ 7/7.
- Mười lăm kịch bản của mục *Kịch bản kiểm thử* trong đặc tả đều nằm trong bộ này: -1 → UAT-1 · -2 → UAT-26 · -3 → UAT-27 · -4 → UAT-29 · -5 → UAT-30 · -6 → UAT-32 · -7 → UAT-18 · -8 → UAT-24 · -9 → UAT-8 · -10 → UAT-20/-21 · -11 → UAT-19 · -12 → UAT-5/-6 · -13 → UAT-16 · -14 (dòng mã CĐT) → UAT-39/-40 · -14 (sửa cột BOQ gốc) → UAT-14/-15.
- **Điểm đã đổi ngày 19/09/2026 theo YC-03.** Đổi tên chỉ chạm vào **tên phiếu** và **nhãn `reviewer`**: các kịch bản UAT-2/-4/-10/-12/-33/-34/-36 được sửa lời cho đúng tên mới, không đổi hành vi kỳ vọng và không đổi mã kịch bản. Cố ý **không** đổi: tên màn hình "Thẩm định BOQ liên phòng ban", trạng thái *Chuyển thẩm định* / *Đang thẩm định* của gói thầu, và tên phiếu **Khảo sát hiện trường** của Ban dự án — ba điểm này được kiểm chốt riêng ở UAT-37 và UAT-43 để một đợt đổi tên sau không kéo chúng đi theo.
- **Khoảng trống của đặc tả.**
  - *Mục Kịch bản kiểm thử của `spec/BID-02.html` đang có hai dòng cùng mang mã `UAT-BID02-14`* — dòng mã CĐT ở chế độ gọn (thêm theo YC-04) và dòng sửa cột BOQ gốc. Bộ này ánh xạ cả hai, nhưng đặc tả cần đánh lại số cho một trong hai. Đây là lỗi đánh số của trang, không phải khoảng trống nghiệp vụ.
  - *Trùng mã CĐT sau khi gộp chỉ được nêu là "hợp lệ", không nêu màn hình có nhắc hay không.* BID-01 có thông điệp nhắc (VAL-16 của trang đó); BID-02 chỉ nói bảng đối chiếu "vẫn tách được". UAT-41 vì vậy chỉ kiểm việc tách đúng và không bị chặn, không kiểm thông điệp nào ở màn hình này.
  - Hai câu hỏi mở của đợt 15/09/2026, `PL-C#Q-41` (tắt cấu phần đơn giá đang có số) và `PL-C#Q-42` (mã trường "phiếu thuộc đợt số"), rơi vào BID-03 và ORD-03, không chạm tới màn hình này — ghi lại để khỏi tìm lại.
- Liên màn hình: **BID-01** (bộ cột của gói, nhãn cần số hoá, hai cột mã hạng mục, dòng khối lượng còn trống), **BID-03** (chi phí biện pháp, lý do bỏ qua sai lệch, mã hệ thống là thứ hợp đồng bám vào), **BID-05** (chốt chặn `chk_qty`), **PF-04** (nhắc quá hạn), **PF-05** (phiên bản BOQ), **PL-A** (trạng thái *Đang thẩm định* giữ nguyên tên), **PL-B** (thao tác "Lập phiếu rà soát, kiểm tra Hồ sơ mời thầu").
