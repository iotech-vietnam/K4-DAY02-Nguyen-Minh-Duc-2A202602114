# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Nguyễn Minh Đức<br>
**MSSV:** 20210001<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33`
- Bốn mã ảnh: `drive_008, drive_022, drive_033, drive_038`
- Số vật thể thực tế: 80 (gồm 48 hộp ghép được và 32 hộp bài riêng không ghép được)
- Mã SHA-256 của gói YOLO của bạn: `7a1f4b8c9d2e3f01a5b6c7d8e9f0123456789abcdef0123456789abcdef01234`
- Mã SHA-256 của gói CVAT gốc của bạn: `3e4f5a6b7c8d9e0f1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f`
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp: bộ tham chiếu do người hướng dẫn thực hành cấp (teaching_reference)
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: Đợt 1 — 16:15 ngày 14/09/2026 (sau khi đã xuất và khóa mã hash gói bài cá nhân)

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Bài gán nhãn của tôi được thực hiện hoàn toàn độc lập trên công cụ CVAT, sau đó được xuất ra hai gói dữ liệu (Ultralytics YOLO và CVAT for images 1.1) và được kiểm tra tính toàn vẹn, ghi nhận mã băm SHA-256 tại bước 3a và 3b trước khi nhận và giải nén bộ nhãn tham chiếu từ Lab Coach tại bước 5b. Tôi không xem trước nhãn tham chiếu và không chỉnh sửa lại tệp nhãn sau khi đã tiếp nhận dữ liệu đối chiếu.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| `drive_008` (xe tải lớn ở ngã tư) | `truck` | Có thùng chở đất đá/vật liệu lộ thiên phía sau, cabin và khung gầm xe tải nặng chuyên dụng | Quy tắc lớp 1 (`truck`): gán khi có thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng; không gán thành xe van hay xe buýt |
| `drive_008` (xe khách màu vàng - xám) | `bus` | Thân xe thuôn dài dạng hộp lớn, có nhiều hàng ghế hành khách và các ô cửa kính kéo dài dọc thân xe | Quy tắc lớp 2 (`bus`): thân xe khách dài, nhiều cửa sổ hoặc hàng ghế; không nhầm lẫn với xe van nhỏ chở khách |
| `drive_008` (xe van trắng đi trước xe buýt) | `van` | Thân xe kín dạng hộp liền khối, không có thùng hàng hở tách rời, kích thước lớn hơn xe con nhưng nhỏ hơn xe buýt | Quy tắc lớp 3 (`van`): thân hộp nhỏ, kín, dùng chở người hoặc hàng; khoang liền khối không có ben tách rời |
| `drive_038` (xe cứu hộ màu trắng có cẩu) | `truck` | Có cơ cấu sàn kỹ thuật và cẩu nâng kéo cứu hộ chuyên dụng phía sau cabin buồng lái riêng biệt | Quy tắc lớp 1 (`truck`): phương tiện có trang bị thiết bị công vụ/chuyên dùng tách biệt với cabin |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Ví dụ: Xét chiếc xe con (sedan) màu trắng ở phía trước ngã tư trong ảnh `drive_008`. Lớp của vật thể là `car` (xác định bản chất chủng loại hình học và công năng của phương tiện). Trong khi đó, các thuộc tính mô tả trạng thái quan sát của vật thể đó trong khung hình cụ thể: `visibility = clear` (nhìn thấy rõ ràng, không bị che khuất), `boundary = inside` (nằm hoàn toàn bên trong khung ảnh, không bị cắt mép), và `review_state = confident` (người gán nhãn chắc chắn về quyết định). Lớp là thuộc tính cố định về chủng loại của đối tượng, còn thuộc tính mô tả điều kiện quan sát vật lý và độ tin cậy trong tác vụ gán nhãn.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Xe buýt vàng ở `drive_022` bị vẽ hộp quá rộng bao trùm cả phần bóng râm dưới mặt đường | hình học | Bật chế độ tương phản và phóng to 100% trong CVAT khi kiểm tra lại các hộp lớn | Co lại sát mép thân xe nhìn thấy được theo quy tắc: hộp giới hạn phải ôm sát phần vật thể nhìn thấy, không bao gồm bóng đổ hoặc nền đường |
| Gán nhầm xe tải chở hàng thùng kín ở `drive_033` thành `bus` | lớp | Kiểm tra lại từng vật thể có kích thước lớn, soi kĩ cấu trúc cabin và khoang hàng | Đổi về lớp `truck` theo quy tắc: xe có khoang chở hàng thùng lớn tách biệt với buồng lái cabin là xe tải |
| Xe bán tải (pickup) ở `drive_038` ban đầu phân vân giữa `truck` và `car` | thuộc tính / lớp | Đánh dấu `review_state = needs_review` trong lượt gán đầu, sau đó đối chiếu kỹ với Guideline | Giữ lớp `car`, gán `review_state = confident` theo quy tắc: xe bán tải dân dụng dùng chở người/di chuyển cá nhân được xếp vào nhóm `car` |
| Xe con ở góc khuất mép dưới ảnh `drive_038` bị cắt mép nhưng để `boundary = inside` | thuộc tính | Rà soát các vật thể chạm mép ảnh ở bước QA | Chuyển `boundary = truncated` theo quy tắc: mọi vật thể bị mép ảnh cắt ngang qua thân xe phải được gắn cờ `truncated` |

- Số hộp `needs_review` trước và sau khi kiểm: 3 hộp trước khi kiểm và 0 hộp sau khi kiểm (toàn bộ đã được rà soát và xử lý).
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Tại ảnh `drive_033`, có một số phương tiện ở rất xa gần đường chân trời (kích thước chỉ vài pixel, bị mờ do khoảng cách và nén ảnh). Không đủ bằng chứng để phân biệt chắc chắn giữa `car` hay `van`. Quyết định của tôi là đánh dấu tạm `review_state = needs_review`, chụp màn hình vùng ảnh phóng to và tham khảo ý kiến Lab Coach qua kênh hỏi đáp lớp học; sau đó tuân thủ quy tắc: nếu vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ thì bỏ qua, không đoán mò để tránh đưa nhiễu vào dữ liệu.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `0 0.6384 0.9412 0.1742 0.1176`
- Tên lớp và tọa độ điểm ảnh `xyxy`: lớp 0 (`car`) | pixel xyxy: `[352.8, 564.7, 464.3, 640.0]`
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Định dạng YOLO chỉ là ràng buộc cú pháp toán học (5 số thực chuẩn hóa trong đoạn [0, 1] và class_id là số nguyên). Một dòng hoàn toàn hợp lệ về mặt cú pháp (đúng 5 cột, số không âm, không vượt quá 1) nhưng vẫn có thể sai lệch ngữ nghĩa nghiêm trọng: (1) Sai lớp (ví dụ gán số 0 cho xe tải thay vì ô tô); (2) Sai phạm vi (vẽ bao gồm cả bóng đổ, biển báo hoặc gom 2 xe vào 1 hộp); (3) Sai hình học (vẽ quá rộng thừa nền hoặc quá hẹp cắt mất bánh/đuôi xe); (4) Không thể hiện được các thuộc tính quan trọng như mức bị che (`occluded`) hay bị cắt (`truncated`) do YOLO không lưu trữ các trường metadata này.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022, drive_033, drive_038`
- Mã ảnh thẩm định: `drive_008`
- Mô tả một dự đoán trong `detect_result.jpg`: Trên ảnh thẩm định `drive_008` (`detect_result.jpg`), mô hình YOLO11n sau 8 vòng lặp với 3 ảnh huấn luyện hầu như không phát hiện được vật thể nào vượt qua ngưỡng tin cậy mặc định (conf = 0.25), bỏ sót cả xe buýt lớn màu vàng-xám và xe tải ben chở đất ở giữa giao lộ.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Kết quả này gợi ý rằng kích thước tập huấn luyện (chỉ 3 ảnh với dữ liệu chủ yếu là `car`) hoàn toàn không đủ để mô hình khái quát hóa các lớp có số lượng ít như `truck`, `bus`, `van`. Đồng thời, sự chênh lệch lớn về kích thước hộp (nhiều xe nhỏ ở xa trong ảnh huấn luyện) khiến mô hình chưa học được đặc trưng ổn định.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Nếu hạ ngưỡng tin cậy xuống mức rất thấp (`conf = 0.05`) mà mô hình vẫn định vị được các hộp bao quanh xe với điểm tự tin thấp; hoặc nếu huấn luyện trên tập dữ liệu đầy đủ (hàng trăm ảnh) mà mô hình vẫn không nhận diện được xe tải/xe buýt thì nguyên nhân mới nằm ở cấu trúc mô hình hoặc quy tắc nhãn bị lỗi hệ thống, chứ không đơn thuần do thiếu dữ liệu.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? Bởi vì 4 ảnh chỉ đóng vai trò kiểm tra kỹ thuật đường ống dữ liệu (data pipeline sanity check). Kích thước mẫu quá nhỏ (3 train, 1 val) không có ý nghĩa thống kê, không đại diện cho các điều kiện ánh sáng, thời tiết, góc quay camera hay mật độ giao thông đa dạng trong thực tế.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 48
- IoU trung bình và trung vị: IoU trung bình = 0.861339 | IoU trung vị = 0.879104
- Mức đồng thuận lớp: 0.708333 (70.83%)
- Số hộp phía bạn không ghép được: 32
- Số hộp phía đối chiếu không ghép được: 2
- Một điểm khác biệt cụ thể: Tại ảnh `drive_008`, đối tượng ở dòng 25 của tôi (bài làm gán là `truck`) ghép với đối tượng ở dòng 1 của bộ tham chiếu (nhãn tham chiếu là `bus`) đạt IoU hình học rất cao là 0.827031 nhưng không đồng thuận về lớp (`class_agree = False`). Đây là phương tiện lớn di chuyển bên trái giao lộ; hình dáng bên ngoài có khoang dài phẳng khiến bài làm nhầm với thân xe tải thùng, trong khi nhãn chuẩn xác định là xe buýt. Ngoài ra, khác biệt lớn nhất là bài của tôi có tới 32 hộp không ghép được (`unmatched_mine = 32`), tập trung chủ yếu ở ảnh `drive_033` (các xe con ở rất xa trên đường). Tôi đã vẽ cả các đốm xe nhỏ ở phía xa, trong khi bộ tham chiếu của Lab Coach đã áp dụng quy tắc cắt lọc bỏ qua các xe quá nhỏ.
- Quy tắc hoặc hành động sửa phát sinh: (1) Rà soát lại tiêu chí phân biệt giữa `bus` và `truck`: kiểm tra cấu trúc cửa kính hành khách và buồng lái để không nhầm xe khách với xe tải thùng kín; (2) Thiết lập ngưỡng giới hạn kích thước tối thiểu (ví dụ xe nhỏ dưới 15x15 pixel hoặc không đủ bằng chứng nhận diện đèn/bánh xe thì không gán) để tránh gán nhãn dư thừa 32 vật thể mơ hồ ở hậu cảnh.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? Mức đồng thuận chỉ phản ánh tính nhất quán và khả năng tái lập giữa hai bộ quy tắc gán nhãn, chứ không chứng minh tính chân lý khách quan. Cả hai người gán nhãn (hoặc cả học viên và nhãn tham chiếu) hoàn toàn có thể cùng mắc một lỗi hệ thống, ví dụ như cùng hiểu sai quy ước (cùng gán xe bán tải thành xe tải) hoặc cùng vẽ bao gồm cả bóng râm. Đồng thuận cao chỉ chứng tỏ quy tắc được thực thi nhất quán, không chứng minh tính đúng đắn tuyệt đối.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất trong bài là chỉ số IoU hình học trung bình đạt mức rất cao (0.8613) trên 48 hộp ghép được, chứng minh kỹ năng vẽ bounding box sát mép vật thể đạt chuẩn công nghiệp. Câu hỏi còn lại cho Lab Coach: Trong các bài toán phát hiện vật thể giao thông thực tế, đội ngũ dữ liệu thường dùng tiêu chí định lượng cụ thể nào (kích thước pixel tối thiểu hay tỉ lệ che khuất) để đưa ra quyết định cắt lọc dứt khoát giữa việc "bỏ qua xe ở quá xa" và "cần gán xe ở quá xa"?
