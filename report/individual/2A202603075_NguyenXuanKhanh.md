# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Nguyễn Xuân Khánh
- MSSV:2A202603075
- Email: khanhblackjaky@gmail.com
- Lớp:K5-L2A
- Link repo bài làm:https://github.com/Khanh-AI-ML-DL/K5-L2A-NguyenXuanKhanh-2A202603075-Day02-ObjectDetection.git
- Link Colab đã chạy:https://colab.research.google.com/drive/1Nilj9Eat90KptL4wDnuc4g62ldL6cnB3#scrollTo=8e715548

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.
Ba ca mơ hồ đã ghi trong guideline:

|Ảnh	|Quyết định|
|drive_018	|Chỉ giữ xe lớn, rõ lớp; xóa box xe nhỏ hoặc che gần hết.|
|drive_041	|Thêm box cho từng xe nhìn thấy ≥30% thân, kể cả xe đỗ sát hoặc bị che một phần.|
|drive_009	|Siết lại box và kiểm lại lớp van/car/truck theo điểm phân biệt.|


## 3. Annotation và self-QC

- Số file nhãn train:50 (checker báo cáo 50 ảnh drive_001–drive_050)
- Kết quả checker và lỗi đã xử lý:chạy tools/evaluate_annotations.py lần 1 cho F1 = 0.5891, chưa đạt (yêu cầu ≥ 0.80). Lỗi chính là gán thừa (FP = 329) và bỏ sót (FN = 243).
- Danh sách ít nhất 10 ảnh đã rà bằng mắt:`drive_001`, `002`, `003`, `018`, `028` (gán thừa); `drive_024`, `027`, `041`, `048`, `050` (bỏ sót); `drive_009`, `030`, `036`, `042` (đủ số box nhưng khớp thấp)
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:
- Commit khóa nhãn ban đầu: a1bede4

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 410| |
| FP | 329| |
| FN | 243| |
| Precision |0.5548 | |
| Recall | 0.6279| |
| F1 | 0.5891| |
| mean_iou_of_matches | 0.8123| |
| passed |false | |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: 
- Các tham số thay đổi so với notebook nếu có và lý do: yolo26n.pt, 50 epoch, imgsz=640, batch=-1, seed=20260909
- Đường dẫn best checkpoint trong phiên chạy:

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision |0.3167586296505923 |
| Recall |0.3904255319148936 |
| mAP50 | 0.3612044317184494|
| mAP50–95 |0.23811656540998277 |

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.
Kết quả Colab (giải nén từ `day2_colab_outputs.zip`) nằm trong [`report/individual/assets/`](assets/), gồm [`eval_predictions/`](assets/eval_predictions/) và [`yolo26_runs/`](assets/yolo26_runs/).

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box? - mAP50 chỉ yêu cầu IoU ≥ 0.5, còn mAP50–95 lấy trung bình đến IoU 0.95. Hai chỉ số lệch nhau nghĩa là xe được phát hiện đúng nhưng box chưa khít. Cần kiểm tra box có quá rộng/quá chặt, bị lệch cạnh, hoặc có nhất quán giữa các ảnh khi xe bị che khuất và bị cắt ở rìa
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì? -Nếu train trên ảnh eval, model đã thấy đáp án nên mAP chỉ đo khả năng ghi nhớ, không đo khả năng tổng quát hóa. Trong notebook, data.yaml trỏ val tới 10 ảnh eval, nên chúng dùng làm validation trong lúc train và để chọn best.pt. Vì vậy đây không phải test set độc lập và mAP có thể hơi lạc quan.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Gold QC lần 1 (đêm 09/11) | F1 = 0.5891 < 0.80; FP = 329, FN = 243 | Đối chiếu số box theo từng ảnh giữa bài làm và gold để chọn ảnh cần rà |  |
| Viết mini guideline | Chưa xem được ảnh để xác nhận tình huống của ba ca mơ hồ | Chọn ca từ số liệu checker (`drive_018`, `drive_041`, `drive_009`) | |
| Huấn luyện YOLO | Chưa chạy vì nhãn chưa qua cổng gold-set | — | Còn thiếu toàn bộ mục 5 |
## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.
- **Đã có:** schema và mini guideline (`GUIDELINE_MINI.md`); đủ 50 file nhãn train; kết quả gold QC lần 1.
- **Chưa đạt/chưa có:** gold QC chưa đạt (F1 0.5891 < 0.80); chưa ghi được rework sau sửa; chưa chạy huấn luyện và đánh giá YOLO; chưa có danh sách ảnh rà bằng mắt, commit khóa nhãn, link repo/Colab.


## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.
