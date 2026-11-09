# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | xe con 4-9 chỗ, xe bán tải, (sedan, SUV, hatchback, MPV)  | Nhỏ hơn xe van, không dài và có thùng hàng như truck, không nhièu chỗ như xe bus |
| `truck` | xe tải thùng thông dụng, xe đầu kéo, ô tô kéo rơ-moóc, có cabin rời và khoang chở hàng phía sau  | Khoang chở hàng lớn lớn hơn xe van, chuyên chở hàng |
| `bus` | xe bus, xe khách thân dài nhiều chỗ ngồi, thường có nhiều cửa sổ, kính lái xe lớn | Thân dài nhiều cửa sổ nhiều chỗ ngồi hơn van |
| `van` | xe hộp cao hơn car, dùng chở người hoặc chở hàng ( ít hơn xe truck), có khoang cabin liền khối  | thông thường lớn hơn car và nhỏ hơn truck, không có nhiều chỗ chứa hàng và thùng hàng rời như truck |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: box bao sát toàn bộ phần nhìn thấy của xe, hạn chế lấy nền thừa và không tính bóng đổ của xe vào box.
- Vật thể bị che khuất: vẫn gán nhãn nếu còn đủ thông tin để nhận biết loại xe; box bao quanh phần xe nhìn thấy được theo guideline.
- Vật thể bị cắt bởi rìa ảnh: vẫn gán nhãn nếu còn đủ đặc điểm để xác định lớp; chỉ vẽ box đến phần vật thể xuất hiện trong ảnh, không ước lượng phần nằm ngoài khung hình.
- Vật thể rất nhỏ/mơ hồ: chỉ gán nhãn khi vẫn có thể xác định được đó là một trong bốn lớp car, truck, bus, van; nếu quá nhỏ hoặc mờ đến mức không xác định được lớp thì không tự đoán điền vào trường hợp mơ hồ.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_006` | chỉ thấy một phần đầu xe, ảnh mờ, khó phân biệt car hay van | Không gán nhãn | vật thể bị cắt khung và thiếu đặc trưng thân xe nên class không rõ |
| `drive_007` | Xe bị cây che một phần, ảnh mờ và không nhìn rõ cấu trúc thân xe; khó phân biệt car hay van.| Không gán nhãn. | Không đủ đặc điểm nhìn thấy để xác định chắc lớp; tránh gán sai class khi vật thể vừa bị che vừa mờ. |
| `drive_017` | Một số xe ở rất xa, kích thước nhỏ và ảnh mờ; khó phân biệt chắc car, van, truck hay bus. | Không gán các xe quá xa/mờ không thể phân loại chắc chắn. | Tránh đoán sai class khi object quá nhỏ hoặc thiếu đặc trưng nhận dạng; chỉ annotate khi còn đủ thông tin thị giác để xác định một trong bốn lớp. |
| `drive_001` | ảnh bị mờ khi kéo dài về phía xa và bị hàng cây che khuất không nhìn rõ cấu trúc xe | Không gán nhãn | Không đủ đặc điểm nhìn thấy để xác định chắc lớp; tránh gán sai class khi vật thể vừa bị che vừa mờ. |
| `drive_004` | hình ảnh chụp vào buổi tối các phương tiện bật đèn và chất lượng hình ảnh mờ và nhỏ làm khó xác định được loại xe để gán nhãn | Không gán nhãn | Tránh đoán sai class khi object quá nhỏ hoặc thiếu đặc trưng nhận dạng, tránh đoán sai vật thể khi bị mờ |
| `drive_009` | những chiếc xe đã bị xe buýt che khuất không xác định được loại xe | Không gán nhãn | Không tự ý đoán khi vật thể bị che gần như hoàn toàn không đủ yếu tố quyết định loại xe. |
| `drive_013` | chỉ thấy một phần đầu xe khó phân biệt truck hay van | Không gán nhãn  | vật thể bị cắt khung và thiếu đặc trưng thân xe nên class không rõ |
| `drive_019` | các xe bị hầm chui che khuất 1 phần và hình ảnh mờ từ đó không trích xuất được dạng xe | Không gán nhãn | Không gán các xe quá xa/mờ không thể phân loại chắc chắn. | Tránh đoán sai class khi object quá nhỏ hoặc thiếu đặc trưng nhận dạng; chỉ annotate khi còn đủ thông tin thị giác để xác định một trong bốn lớp. |