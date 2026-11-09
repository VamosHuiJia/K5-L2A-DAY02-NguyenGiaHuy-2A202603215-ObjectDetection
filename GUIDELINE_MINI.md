# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con, sedan, SUV, taxi và xe bán tải; dùng ID 0. | Phân loại theo loại xe, không theo trạng thái đang chạy hay đỗ. Xe bán tải vẫn thuộc `car` theo schema của bài. |
| `truck` | Xe tải chở hàng và xe đầu kéo; dùng ID 1. | Nhận diện qua cabin và phần thùng hoặc sàn chở hàng; không xếp xe bán tải vào lớp này. |
| `bus` | Xe buýt, xe khách và minibus chở khách; dùng ID 2. | Xe khách có thân dài, nhiều hàng cửa sổ hoặc hình dáng xe buýt; không xếp van/minivan vào lớp này chỉ vì xe chở người. |
| `van` | Xe van, minivan chở hàng hoặc chở người; dùng ID 3. | Thân xe dạng hộp, khoang hàng hoặc khoang khách liền với thân xe; phân biệt với xe con thông thường và xe buýt theo hình dáng quan sát được. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: mỗi xe hợp lệ có một box chữ nhật riêng, bao sát các phần xe nhìn thấy được, hạn chế lấy thêm nền và không gộp hai xe vào một box.
- Vật thể bị che khuất: vẫn gán nhãn nếu phần nhìn thấy đủ để nhận diện lớp; box bao phần nhìn thấy của từng xe, không suy đoán phần thân bị che. Hai xe chồng lên nhau vẫn có hai box riêng.
- Vật thể bị cắt bởi rìa ảnh: gán nhãn nếu còn nhận diện được lớp, đặt cạnh box tại rìa ảnh và không kéo box ra ngoài hoặc đoán phần xe nằm ngoài khung hình.
- Vật thể rất nhỏ/mơ hồ: phóng to và kiểm tra hình dáng trước khi quyết định; nếu chưa xác định chắc lớp thì ghi tên ảnh, vị trí và điểm chưa rõ để hỏi giảng viên, không tự đoán hoặc tự đặt ngưỡng kích thước. Chỉ gán bốn lớp của schema; không gán người, xe máy, xe đạp, phản chiếu hoặc hình xe trên quảng cáo.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001.jpg` | Xe nhỏ màu trắng, thân dạng hộp ở phía trái ảnh dễ nhầm với xe con do kích thước nhỏ. | Gán `van` (ID 3), box sát phần xe nhìn thấy. | Dựa vào hình dáng thân xe dạng hộp và khoang xe liền khối; không phân loại thành `car` chỉ vì xe nhỏ. |
| `drive_009.jpg` | Xe khách màu trắng ở phía trên bên phải ảnh có thể nhầm với `van` vì cùng chở người. | Gán `bus` (ID 2), mỗi xe khách hoặc xe buýt có một box riêng. | Xe có thân dài và nhiều hàng cửa sổ, phù hợp lớp xe khách/xe buýt; mục đích chở người không đủ để kết luận là `van`. |
| `drive_042.jpg` | Xe chở nhiều bao hàng ở phía phải ảnh, hàng hóa che khuất phần lớn thân xe và làm hình dáng tổng thể khó nhận diện. | Gán `truck` (ID 1) theo phần cabin và đặc điểm chở hàng còn nhìn thấy; không suy đoán phần xe bị che. | Đây là xe tải chở hàng; không phân loại theo kích thước lớn đơn thuần hoặc nhầm với xe buýt chạy bên cạnh. |
| `drive_002.jpg` | Xe khách màu trắng ở gần giữa ảnh có phần bóng đổ kéo dài trên mặt đường. | Gán `bus` (ID 2), box sát thân xe và không lấy thêm bóng đổ. | Bóng đổ không phải phần thân xe; hình dáng xe khách phù hợp lớp `bus`. |
| `drive_003.jpg` | Các xe con và taxi ở giữa giao lộ đứng sát nhau, một số phần thân che lẫn nhau. | Gán `car` (ID 0) cho từng xe nhận diện được, mỗi xe một box riêng. | Các xe vẫn là những vật thể độc lập; không gộp cả hàng xe vào một box hoặc đoán phần bị che. |
| `drive_004.jpg` | Ảnh ban đêm có đèn xe và các vệt sáng phản chiếu trên đường, dễ làm box rộng hơn thân xe. | Gán xe nhận diện được theo loại xe; không tạo box cho vệt sáng và không kéo box theo quầng đèn. | Ánh sáng và phản chiếu không thuộc bốn lớp; box cần bám phần xe nhìn thấy. |
| `drive_005.jpg` | Mặt đường ướt tạo bóng phản chiếu ngay dưới các xe đang đi qua giao lộ. | Mỗi xe thật chỉ có một box; không gán thêm box cho hình phản chiếu dưới xe. | Phản chiếu không phải một phương tiện độc lập trong cảnh. |
| `drive_006.jpg` | Xe màu trắng nhỏ ở nửa dưới, bên trái ảnh có dáng ngắn và phần mui cao, dễ nhầm với van nhỏ. | Gán `car` (ID 0) theo hình dáng xe con; không đổi lớp chỉ vì thân xe ngắn hoặc mui cao. | Xe con cỡ nhỏ vẫn thuộc `car`; không phải mọi xe mui cao đều là `van`. |
| `drive_008.jpg` | Xe máy, xe đạp và người đi đường xuất hiện xen giữa các xe ô tô tại giao lộ. | Chỉ gán các xe thuộc `car`, `truck`, `bus`, `van`; không gán người, xe máy và xe đạp. | Các vật thể này nằm ngoài phạm vi bốn lớp bắt buộc của bài. |
| `drive_014.jpg` | Xe cứu hộ màu trắng có cabin và thiết bị nâng phía sau, khác hình dáng xe tải thùng thông thường. | Gán `truck` (ID 1) theo kết cấu xe tải; không xếp vào `van` vì màu sắc hoặc cabin nhỏ. | Phân biệt bằng cấu tạo phương tiện; xe không có khoang liền kín như van. |
| `drive_016.jpg` | Xe con màu tối ở góc dưới bên phải bị nhòe chuyển động và cắt bởi mép dưới ảnh. | Gán `car` (ID 0) khi còn nhận diện được thân xe; box nằm trong ảnh, không đoán phần ngoài khung. | Nhòe hoặc cắt khung không làm thay đổi loại xe; chỉ sử dụng phần quan sát được. |
| `drive_023.jpg` | Xe taxi ở sát mép trên ảnh chỉ hiện một phần, có thể bị bỏ qua vì chưa thấy toàn bộ xe. | Gán `car` (ID 0) cho phần xe nhìn thấy, giữ box trong giới hạn ảnh. | Taxi thuộc `car`; xe bị cắt khung vẫn cần nhãn nếu đủ dấu hiệu nhận diện lớp. |
| `drive_030.jpg` | Xe màu tối, thân dài và cao ở phía dưới bên phải ảnh dễ nhầm giữa minivan và SUV. | Gán `van` (ID 3) theo thân xe liền khối và khoang khách kéo dài. | Phân biệt theo hình dáng van/minivan, không dựa riêng vào màu xe hoặc việc chở người. |
| `drive_038.jpg` | Xe tải nhỏ cabin xanh ở phía trên bên phải có thùng hàng che bạt, dễ nhầm với van vì kích thước nhỏ. | Gán `truck` (ID 1), tách biệt với chiếc van màu đỏ ngay cạnh. | Xe có cabin và thùng chở hàng riêng; hai xe sát nhau vẫn phải có hai box. |
| `drive_043.jpg` | Xe màu trắng, thân hộp cao ở gần giữa ảnh có thể nhầm với xe buýt nhỏ khi nhìn từ phía trước. | Gán `van` (ID 3) theo thân xe và khoang xe liền khối. | Không xếp vào `bus` chỉ vì xe cao; cần xét tổng thể hình dáng van/minivan. |
| `drive_050.jpg` | Người đi bộ và người đi xe đạp xuất hiện sát các xe ô tô ở giữa và bên phải ảnh. | Không tạo nhãn cho người hoặc xe đạp; chỉ vẽ box riêng cho ô tô thuộc schema. | Các đối tượng ngoài phạm vi không được gán thành xe hoặc gộp vào box xe bên cạnh. |
