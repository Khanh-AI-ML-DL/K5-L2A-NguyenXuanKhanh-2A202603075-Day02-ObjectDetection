# Mini guideline — bài làm cá nhân

# GUIDELINE_MINI

## Định nghĩa lớp

| Lớp | Định nghĩa | Ví dụ / điểm phân biệt |
|---|---|---|
| car (0) | Xe con chở người, gầm thấp, 4-5 chỗ: sedan, hatchback, SUV nhỏ | Mui thấp, không có thùng hàng; SUV/crossover vẫn là car |
| truck (1) | Xe tải, xe bán tải có thùng hàng hở hoặc kín, phần đầu cabin tách khỏi thùng | Có thùng chở hàng phía sau; pickup tính là truck |
| bus (2) | Xe khách/xe buýt cỡ lớn, nhiều cửa sổ dọc thân, chiều dài rõ rệt | Dài hơn hẳn car; minibus 16+ chỗ tính là bus |
| van (3) | Xe thân liền khối, cao, hộp vuông, chở người (7-16 chỗ) hoặc hàng nhẹ | Cao hơn car, không có cabin tách như truck; MPV/minivan tính là van |

## Quy tắc chung

- **Box bám sát vật thể:** cạnh box chạm phần ngoài cùng của thân xe nhìn thấy được (gồm gương, không gồm bóng đổ), không chừa viền thừa.
- **Vật thể bị che khuất:** vẽ box quanh phần nhìn thấy nếu còn thấy khoảng 30% thân xe trở lên; che nhiều hơn thì bỏ qua.
- **Vật thể bị cắt bởi rìa ảnh:** vẽ box đến sát rìa ảnh nếu còn thấy đủ đặc trưng để nhận ra loại xe.
- **Vật thể rất nhỏ/mơ hồ:** xe quá nhỏ (dưới khoảng 15 px mỗi chiều) hoặc không phân biệt được loại thì không gán; mơ hồ giữa hai lớp thì chọn theo bảng "điểm phân biệt" ở trên và ghi vào bảng dưới.

## Ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_018` | Gold chỉ có 2 box nhưng bài làm có 10 box, chỉ khớp 1 (9 box dư): nhiều khả năng đã gán nhãn xe ở xa, rất nhỏ hoặc bị che gần hết. | Chỉ giữ xe lớn, rõ lớp; xóa các box xe nhỏ/che gần hết. | Theo quy tắc "rất nhỏ/mơ hồ" và "che khuất": dưới ~30% thân nhìn thấy hoặc box nhỏ hơn ~12×12 px thì không gán nhãn, tránh false positive. |
| `drive_041` | Gold có 24 box nhưng bài làm chỉ 12 box, khớp 5 (19 box thiếu): nhiều khả năng cảnh đông xe, xe đỗ sát nhau hoặc che nhau bị bỏ sót. | Thêm box cho từng xe nhìn thấy ≥30% thân, kể cả xe đỗ sát hoặc che một phần; mỗi xe một box. | Theo quy tắc "vật thể bị che khuất": xe che một phần vẫn phải gán nhãn, box chỉ bao phần nhìn thấy, giảm false negative. |
| `drive_009` | Số box gần bằng nhau (22 so với 21) nhưng chỉ khớp 8: nhiều khả năng box lỏng/lệch (IoU < 0.5) hoặc nhầm lớp `van`/`car`/`truck`. | Siết lại box sát phần nhìn thấy; xe có khoang hộp liền thân và nóc cao → `van`, xe có cabin tách thùng → `truck`, còn lại `car`. | Theo "box bám sát" và bảng phân biệt lớp: sửa đúng lớp và tọa độ để chuyển FP+FN thành TP. |
