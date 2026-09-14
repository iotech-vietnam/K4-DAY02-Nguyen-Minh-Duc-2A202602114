# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Nguyễn Minh Đức<br>
**MSSV:** 20210001<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: `drive_008`, phương tiện màu bạc/trắng di chuyển trước đầu xe buýt vàng.
- Dấu hiệu nhìn thấy: Thân xe dạng hộp nhỏ gọn, có kính hai bên hông nhưng kích thước ngắn, không có cấu trúc xe khách đường dài hay nhiều dãy ghế như xe buýt lớn.
- Quy tắc áp dụng: Quy tắc lớp 3 (`van`): xe thân hộp nhỏ kín, dùng chở người hoặc hàng; phân biệt với lớp 2 (`bus`) vốn dành cho xe khách lớn dài có nhiều ô cửa sổ.
- Quyết định: Gán nhãn `van`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Nếu khoảng cách quá xa hoặc bị che khuất không nhìn rõ cửa hông, đánh dấu `review_state = needs_review`, phóng to 100% kiểm tra chiều dài trục cơ sở so với các xe con xung quanh; nếu vẫn không đủ bằng chứng tin cậy thì trao đổi với Lab Coach.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038`, xe cứu hộ màu trắng có cơ cấu cẩu móc ở giữa ngã tư.
- Dấu hiệu nhìn thấy: Phần cabin buồng lái tách rời, phía sau có sàn phẳng và gắn khung kim loại kèm cẩu nâng/móc kéo chuyên dụng.
- Quy tắc áp dụng: Quy tắc lớp 1 (`truck`): phương tiện có trang bị thùng hàng, ben hoặc thiết bị công vụ/chuyên dùng rõ ràng tách biệt với khoang lái.
- Quyết định: Gán nhãn `truck`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu `needs_review` để kiểm tra lại hướng dẫn cụ thể của bài lab đối với xe công vụ đặc chủng; nếu xe dạng bán tải thông thường không có cơ cấu sàn cẩu thì ưu tiên xếp vào `car`.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_038`, xe con màu sẫm ở mép dưới bên phải bức ảnh.
- Dấu hiệu nhìn thấy khi phóng 100%: Nhìn thấy một phần nắp cốp sau và đèn hậu xe, phần thân trước và nửa xe phía trên bị mép ảnh cắt ngang.
- Giá trị `visibility`: `clear` (phần nhìn thấy được nét rõ ràng, không bị vật thể khác che chắn).
- Giá trị `boundary`: `truncated` (vật thể bị cắt ngang bởi đường biên của ảnh).
- Trạng thái `review_state`: `confident` (đủ đặc trưng đuôi xe và đèn để khẳng định là ô tô con).
- Lý do: Vật thể tuy bị biên ảnh cắt (`truncated`) nhưng phần còn lại trong ảnh vẫn đủ dấu hiệu nhận diện đặc thù của `car`, nên vẫn gán hộp ôm sát phần nhìn thấy và đánh dấu thuộc tính chính xác.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [x] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 80 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
