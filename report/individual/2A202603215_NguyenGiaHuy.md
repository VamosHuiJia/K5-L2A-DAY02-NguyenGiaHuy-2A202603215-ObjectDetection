# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyễn Gia Huy
- MSSV: 2A202603215
- Email: giahuy20092004@gmail.com
- Lớp: L2A
- Link repo bài làm: https://github.com/VamosHuiJia/K5-L2A-DAY02-NguyenGiaHuy-2A202603215-ObjectDetection.git
- Link Colab đã chạy: https://colab.research.google.com/drive/1etDjL1ivMTkt13jOzroW45pb2jChm7yG?usp=sharing

## 2. Schema và mini guideline

Áp dụng schema cố định: `car=0`, `truck=1`, `bus=2`, `van=3`. Xe bán tải thuộc `car`; không gán người, xe máy, xe đạp hoặc các vật thể ngoài bốn lớp.

[GUIDELINE_MINI.md](../../GUIDELINE_MINI.md) ghi định nghĩa lớp, quy tắc box và 16 ca mơ hồ. Mỗi xe hợp lệ có một box riêng, bám phần nhìn thấy; xe bị che hoặc cắt khung vẫn được gán nếu còn nhận diện được lớp. Không suy đoán phần thân bị che hoặc nằm ngoài ảnh.

Ba ca tiêu biểu:

| Ảnh | Quyết định | Căn cứ |
|---|---|---|
| `drive_001.jpg` | Xe nhỏ màu trắng, thân hộp phía trái ảnh được gán `van` (ID 3). | Xét hình dáng và khoang xe liền khối, không dựa riêng vào kích thước nhỏ. |
| `drive_009.jpg` | Xe khách màu trắng ở phía trên bên phải được gán `bus` (ID 2). | Thân dài và nhiều hàng cửa sổ phù hợp lớp xe khách/xe buýt. |
| `drive_042.jpg` | Xe chở nhiều bao hàng phía phải ảnh được gán `truck` (ID 1). | Dựa vào cabin và kết cấu chở hàng còn nhìn thấy. |

Điểm cần giảng viên làm rõ là sự tương thích giữa ID lớp trong gold với schema của đề và phạm vi vật thể được gold bao phủ. Các bằng chứng cụ thể được ghi ở mục 4; chưa có xác nhận của giảng viên được cung cấp cho báo cáo.

## 3. Annotation và self-QC

- Số file nhãn train: **50 file**, từ `drive_001.txt` đến `drive_050.txt`, tương ứng 50 ảnh train. Bộ nhãn ban đầu có **582 box**; sau rework có **689 box**.
- Công cụ annotation: **CVAT local chạy bằng Docker**. Bộ nhãn ban đầu được học viên gán thủ công trên toàn bộ 50 ảnh; vòng rework có ChatGPT/Codex hỗ trợ đối chiếu ảnh và cập nhật annotation trên CVAT. Sau đó học viên xuất lại bằng **Ultralytics YOLO Detection 1.0** và chạy đánh giá với gold gốc.
- Kết quả checker: chạy `python -X utf8 tools/check_yolo_labels.py`, kết quả **ĐẠT cấu trúc: 50/50 file nhãn hợp lệ**. Không còn lỗi cấu trúc được checker báo ở bản hiện tại. Checker không đánh giá độ đầy đủ, độ sát box hoặc tính đúng đắn của lớp trên ảnh.
- Danh sách ảnh đã quan sát bằng mắt: toàn bộ `drive_001.jpg`–`drive_050.jpg` trong quá trình gán nhãn thủ công. Các ảnh đại diện có tình huống được ghi lại trong guideline gồm `drive_001`, `drive_002`, `drive_003`, `drive_004`, `drive_005`, `drive_006`, `drive_008`, `drive_009`, `drive_014`, `drive_016`, `drive_023`, `drive_030`, `drive_038`, `drive_042`, `drive_043`, `drive_050`. Đây là việc quan sát trong quá trình gán nhãn; chưa có nhật ký riêng xác nhận một lượt self-QC sau xuất nhãn.
- Lỗi/vấn đề phát hiện khi đối chiếu: có xe bị bỏ sót, box gộp nhiều xe, một số trường hợp cần sửa lớp và các bất đồng với gold. Đã cập nhật nhãn trên 40 ảnh, bổ sung 107 box, sửa 3 trường hợp phân lớp và xử lý box gộp hai xe buýt ở `drive_019`; đã xuất lại và test lại. Kết quả trước/sau và các vấn đề còn lại ở mục 4.
- Commit chứa bộ nhãn ban đầu: [`6d761168bd1d2aa6a82125993e6833baf4d1027e`](https://github.com/VamosHuiJia/K5-L2A-DAY02-NguyenGiaHuy-2A202603215-ObjectDetection/commit/6d761168bd1d2aa6a82125993e6833baf4d1027e), thông điệp `feat: add training labels for object detection dataset`. Cần đối chiếu mốc khóa của lớp; lịch sử Git chỉ xác nhận lần lưu nhãn, chưa xác nhận thời điểm nhận gold.

Phân bố box trong nhãn train hiện tại:

| Lớp | Số box |
|---|---:|
| `car` | 543 |
| `truck` | 21 |
| `bus` | 63 |
| `van` | 62 |

`car` chiếm khoảng 78,8% trong tổng 689 box sau rework. Sự chênh lệch này cần được cân nhắc khi phân tích dữ liệu theo lớp. Kết quả YOLO ở mục 5 thuộc lần chạy Colab đã cung cấp trước vòng rework này.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau rework và test lại với gold gốc |
|---|---:|---:|
| TP | 348 | 440 |
| FP | 234 | 249 |
| FN | 305 | 213 |
| Precision | 0.5979 | 0.6386 |
| Recall | 0.5329 | 0.6738 |
| F1 | 0.5636 | 0.6557 |
| mean_iou_of_matches | 0.8292 | 0.8630 |
| passed | false | false |

Nguồn số liệu: hai log chạy evaluator trước/sau do học viên cung cấp, được đối chiếu lại với toàn bộ 50 file nhãn hiện tại. Lần test lại dùng **gold gốc trong `gold_train_labels/`**, giữ nguyên `data.yaml`, script và các ngưỡng chấm. Script ghép box cùng lớp theo IoU >= 0.50, mỗi box được ghép tối đa một lần.

Lệnh đã dùng để test lại:

```powershell
python tools/evaluate_annotations.py --student-labels dataset/labels/train --gold-labels gold_train_labels --output outputs/annotation_evaluation.json
```

File kết quả cần nộp theo đề là `outputs/annotation_evaluation.json`. Tại thời điểm cập nhật báo cáo, file này không còn trong thư mục làm việc; cần chạy lại lệnh trên để lưu lại JSON trước khi nộp. Báo cáo chỉ sử dụng số liệu đánh giá với gold gốc.

**Trạng thái sau test lại: chưa đạt cổng annotation (`passed=false`).** F1 tăng từ 0.5636 lên 0.6557, mean IoU tăng từ 0.8292 lên 0.8630. Mean IoU đã vượt 0.70, nhưng F1 vẫn dưới 0.80 nên chưa đạt đồng thời hai điều kiện của lab.

Sau rework, TP tăng 92 và FN giảm 92, cho thấy số box ghép được với gold đã tăng. FP cũng tăng từ 234 lên 249, vì vậy cần tiếp tục rà các box chưa khớp; không kết luận nguyên nhân chỉ từ tổng FP/FN. Mức tăng F1 là 0.0921, tương đương 9,21 điểm phần trăm.

Các kết quả cụ thể trước/sau:

| Ảnh | Box học viên trước → sau | Số box khớp trước → sau | Nhận xét |
|---|---:|---:|---|
| `drive_005` | 18 → 24 | 13 → 19 | Bổ sung các xe còn thiếu; số matches tăng 6. |
| `drive_010` | 4 → 5 | 4 → 5 | Đã khớp đủ 5/5 box sau khi bổ sung xe sát mép ảnh. |
| `drive_016` | 10 → 17 | 8 → 16 | Bổ sung box và rà lớp; số matches tăng 8. |
| `drive_019` | 19 → 22 | 14 → 16 | Tách hai xe buýt bị gộp, bổ sung xe và rà phân lớp. |
| `drive_024` | 16 → 20 | 11 → 15 | Số matches tăng 4. |
| `drive_026` | 26 → 33 | 20 → 26 | Số matches tăng 6; vẫn còn 13 box gold chưa khớp. |
| `drive_027` | 17 → 23 | 12 → 18 | Số matches tăng 6; vẫn còn 13 box gold chưa khớp. |
| `drive_041` | 13 → 21 | 6 → 14 | Số matches tăng 8. |

Hiện 48/50 ảnh còn box chưa khớp; `drive_010` khớp 5/5 và `drive_013` khớp 4/4. Các ảnh còn nhiều bất đồng cần ưu tiên gồm `drive_002`, `drive_009`, `drive_003`, `drive_012`, `drive_024`, `drive_026`, `drive_027`. Việc rà tiếp cần dựa vào ảnh, lớp và vị trí box, không chỉ so số lượng.

Các vấn đề cụ thể đã kiểm tra:

| Ảnh/vấn đề | Dữ kiện quan sát | Hướng xử lý |
|---|---|---|
| Thứ tự lớp khi xuất CVAT | `data.yaml` trong bản xuất ban đầu xác nhận `0: car`, `1: truck`, `2: bus`, `3: van`; bản rework giữ nguyên schema này. | Giữ nguyên ánh xạ theo đề; không đổi ID hàng loạt chỉ để tăng điểm matching. |
| `drive_009` | Box xe buýt lớn phía trước mang ID 2 trong nhãn học viên, box gold chồng khớp mang ID 3; IoU khoảng 0.968. Theo schema đề, ID 3 là `van`. | Cần hỏi giảng viên về ánh xạ lớp trong gold trước khi quyết định sửa. |
| `drive_042` | Xe tải chở hàng mang ID 1 trong nhãn học viên, box gold chồng khớp mang ID 2; IoU khoảng 0.808. Theo schema đề, ID 2 là `bus`. | Cần xác nhận cách diễn giải ID gold; không sửa file gold. |
| `drive_001` | Trước rework có 15 box, sau rework có 16; gold có 5 và cả 5 vẫn đã khớp. Có các xe bên kia dải phân cách/dọc lề và xe nhỏ phía xa ngoài nhóm gold bao phủ. | Cần làm rõ phạm vi gán nhãn hoặc vùng bỏ qua; không xóa box xe hợp lệ chỉ để bằng số lượng gold. |
| `drive_026`, `drive_041`, `drive_024`, `drive_027` | Sau test lại, số box gold chưa khớp lần lượt là 13, 10, 15, 13. | Tiếp tục mở ảnh để phân biệt bỏ sót, khác lớp và lệch box; cần xác nhận các bất đồng về gold. |

**Đã thực hiện một vòng rework và đã chạy lại gold QC.** Bộ nhãn sau sửa có 689 box, tăng 107 box so với ban đầu; nhãn đã được cập nhật trên CVAT, xuất lại dưới định dạng Ultralytics YOLO Detection 1.0 và kiểm tra cấu trúc đạt 50/50. Gold gốc và script chấm không bị chỉnh sửa trong vòng đánh giá này.

Kế hoạch tiếp theo: xác nhận các bất đồng về lớp và phạm vi gold với giảng viên; tiếp tục sửa những lỗi có căn cứ trên ảnh, xuất lại nhãn và đánh giá lại với gold gốc. Giữ commit ban đầu để đối chiếu. Kết quả hiện tại được báo cáo là chưa đạt, không thay bằng số liệu của một phép đánh giá dùng đầu vào khác.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab. Cell đánh giá dùng **GPU Tesla T4 (14913 MiB), Python 3.13.16, Ultralytics 8.4.208, Torch 2.14.1+cu130**. Output cell train đang ghi **CPU Intel Xeon 2.20 GHz, Python 3.13.18, Torch 2.14.1+cpu**; sự khác biệt giữa hai output được ghi ở mục 6.
- Các tham số thay đổi so với notebook nếu có và lý do: Giữ nguyên các tham số huấn luyện chính: `yolo26n.pt`, `epochs=50`, `imgsz=640`, `batch=-1`, `seed=20260909`, `plots=True`. Điền `REPO_URL` bằng link repo cá nhân để Colab lấy ảnh và nhãn bài làm; giữ nguyên split train/eval.
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.409064 (40.91%) |
| Recall | 0.233936 (23.39%) |
| mAP50 | 0.270667 (27.07%) |
| mAP50–95 | 0.199550 (19.96%) |

Nguồn số liệu: [model_eval_metrics.json](assets/model_eval_metrics.json), ghi nhận đánh giá trên 10 ảnh eval. Hai thư mục kết quả đã có: [eval_predictions/visuals](assets/eval_predictions/visuals/) và [yolo26_runs](assets/yolo26_runs/).

Bảng chỉ số từng lớp đọc từ output cell đánh giá trên Colab; giữ nguyên mức làm tròn của log:

| Lớp | Số box eval | Precision | Recall | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|---:|
| `car` | 94 | 0.836 | 0.652 | 0.740 | 0.524 |
| `truck` | 5 | 0.663 | 0.200 | 0.240 | 0.206 |
| `bus` | 2 | 0 | 0 | 0.0141 | 0.00466 |
| `van` | 12 | 0.136 | 0.0833 | 0.0889 | 0.0635 |

Minh chứng chính: [đường cong Recall–Confidence](assets/yolo26_runs/vehicle_eval/BoxR_curve.png), [đường cong Precision–Recall](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png), [confusion matrix](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [confusion matrix chuẩn hóa](assets/yolo26_runs/vehicle_eval/confusion_matrix_normalized.png), [biểu đồ huấn luyện](assets/yolo26_runs/vehicle_annotation/results.png).

Các ảnh dự đoán trên đủ 10 ảnh eval:

| Ảnh | Quan sát từ output dự đoán |
|---|---|
| [drive_051](assets/eval_predictions/visuals/drive_051.jpg) | Xe con màu đỏ ở tiền cảnh bị dự đoán `van` với confidence 0.99, minh họa nhầm lớp dù confidence cao. |
| [drive_052](assets/eval_predictions/visuals/drive_052.jpg) | Xe buýt hai tầng lớn được dự đoán `bus` với confidence 0.49. |
| [drive_053](assets/eval_predictions/visuals/drive_053.jpg) | Khu vực xe buýt lớn có các box chồng lấp, cần rà cả lớp và hình học box. |
| [drive_054](assets/eval_predictions/visuals/drive_054.jpg) | Cảnh tối và đèn xe; có dự đoán `van` confidence 0.97 ở xe màu đỏ phía dưới ảnh, cần đối chiếu lớp theo hình dáng. |
| [drive_055](assets/eval_predictions/visuals/drive_055.jpg) | Xe con gần camera được dự đoán `car` confidence 0.58; các xe xa có box nhỏ hơn. |
| [drive_056](assets/eval_predictions/visuals/drive_056.jpg) | Cảnh nhiều loại xe có các dự đoán `car`, `bus`, `van`; cần phân biệt chất lượng box và độ đúng lớp. |
| [drive_057](assets/eval_predictions/visuals/drive_057.jpg) | Model tạo nhiều dự đoán `car`; các xe nhỏ, xa có confidence thấp hơn một số xe gần. |
| [drive_058](assets/eval_predictions/visuals/drive_058.jpg) | Cảnh đông xe, có cả dự đoán `car` và `bus`; minh họa việc phải đối chiếu ID nhãn eval trước khi diễn giải lỗi theo lớp. |
| [drive_059](assets/eval_predictions/visuals/drive_059.jpg) | Xe màu tối gần camera bị nhòe chuyển động nhưng vẫn được dự đoán `car` confidence 0.87. |
| [drive_060](assets/eval_predictions/visuals/drive_060.jpg) | Xe khách màu trắng phía trên ảnh được dự đoán `bus` confidence 0.88. |

Trong log train, mAP50–95 validation cao nhất là 0.19993 ở epoch 1, còn epoch 50 là 0.14371. Việc chạy thêm epoch và giảm train loss chưa tạo ra cải thiện validation trong lần chạy này. Bộ dữ liệu nhỏ, mất cân bằng lớp và các bất đồng nhãn cần được xem xét; chưa đủ dữ kiện để quy toàn bộ kết quả thấp cho một nguyên nhân.

Kết quả YOLO đã chạy được, nhưng JSON annotation hiện vẫn chưa đạt cổng CP5. Cần hoàn thiện gold QC trước khi xác nhận đáp ứng đầy đủ trình tự CP5 → CP6 của lab.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?

   Theo bảng metrics từng lớp của cell đánh giá trên Colab, `bus` có recall **0**, thấp nhất; tiếp theo là `van` với 0.0833, `truck` với 0.200 và `car` với 0.652. Đây là số liệu của bước validation; các ảnh prediction được tạo riêng với `conf=0.25`.

   Trong `drive_058`, box eval mang ID 2 ở vùng bên phải, tâm chuẩn hóa khoảng `(0.9375, 0.3969)`, có dự đoán `car` tại khu vực tương ứng thay vì `bus`. Ví dụ này mô tả bất đồng theo ID nhãn đang dùng; cần xác nhận ánh xạ eval/gold trước khi kết luận về loại phương tiện thực tế. Tập eval chỉ có 2 box mang ID 2 nên nhận xét về lớp này cũng dựa trên rất ít mẫu.

2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?

   Cần kiểm các cạnh box có sát vật thể không: box quá rộng/hẹp, lệch tâm, lấy thêm nền, cắt mất phần xe nhìn thấy hoặc xử lý che khuất/cắt khung chưa nhất quán. Box có thể đạt IoU 0.50 nhưng không đạt các ngưỡng cao hơn, làm mAP50–95 thấp hơn. Trong lần chạy này, cả mAP50 và mAP50–95 đều còn thấp; chênh lệch hai chỉ số chưa đủ để khẳng định hình học box là nguyên nhân duy nhất.

3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?

   Nhầm `car`/`van` là trường hợp cụ thể cần rà: xe con màu đỏ ở tiền cảnh `drive_051` được dự đoán `van` confidence 0.99. Guideline cần phân biệt hình dáng xe con/SUV với van/minivan, tránh dựa riêng vào thân cao hoặc mục đích chở người. Confusion matrix cũng có bất đồng giữa `bus` và `van`; cần kiểm cả cách gán lớp lẫn tính tương thích của ID gold/eval với schema. Bộ nhãn của lần chạy Colab đã cung cấp trước rework có 450 `car`, 19 `truck`, 54 `bus`, 59 `van`, nên số mẫu theo lớp là một yếu tố khác cần cân nhắc. Chưa có kết quả huấn luyện lại với bộ 689 box sau sửa được cung cấp cho báo cáo này.

4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?

   Giữ nguyên split để tránh dùng cùng ảnh làm dữ liệu cập nhật trọng số và làm dữ liệu đánh giá. Notebook dùng 10 ảnh eval làm validation trong quá trình train và chọn `best.pt`, sau đó đánh giá lại checkpoint trên chính tập này. Vì đã tham gia lựa chọn checkpoint, eval không phải test set độc lập; kết quả chỉ phản ánh tập validation nhỏ của bài lab và chưa chứng minh khả năng tổng quát trên dữ liệu mới.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Xuất nhãn từ CVAT | Cần kiểm tra thứ tự lớp và cấu trúc file trước khi đánh giá. | Đối chiếu `data.yaml` trong ZIP với schema và chạy checker trên 50 nhãn. | Thứ tự xuất đúng, checker đạt 50/50; chưa có bằng chứng lỗi ánh xạ ở bản xuất CVAT. |
| Chạy script trên Windows | Python gặp lỗi `UnicodeEncodeError` khi in tiếng Việt trong terminal ở bước kiểm tra trước đó. | Dùng tùy chọn `python -X utf8`. | Checker chạy được và in kết quả tiếng Việt. |
| Gold QC và test lại | F1 ban đầu 0.5636 chưa đạt; còn box thiếu, vấn đề hình học/phân lớp và bất đồng với gold. | Rework trên CVAT, xuất lại Ultralytics YOLO Detection 1.0, chạy checker và evaluator với gold gốc. | Checker đạt 50/50; F1 tăng lên 0.6557, mean IoU 0.8630, `passed=false`. Cần tiếp tục rà các bất đồng với giảng viên; không sửa gold. |
| Huấn luyện/đánh giá | Model có recall và mAP thấp; validation không cải thiện về cuối phiên. | Kiểm tra JSON, log 50 epoch, đường cong và ảnh prediction. | Đã có kết quả thực tế; cần phân tích tiếp sau khi làm rõ vấn đề nhãn. |
| Chia sẻ Colab | Link từng yêu cầu quyền truy cập. | Kiểm tra lại notebook được chia sẻ và đối chiếu output đánh giá với file JSON trong `assets/`. | Đã đọc được bản notebook và output đã lưu qua đường tải công khai, không cần đăng nhập; JSON đánh giá trùng kết quả trên máy. |
| Ghi nhận môi trường chạy | Ảnh runtime chọn T4; cell đánh giá ghi Tesla T4, trong khi output cell train còn ghi CPU và phiên bản Python/Torch khác. | Đọc header log từng cell, metadata notebook và `args.yaml`; đối chiếu JSON đánh giá. | Đã xác nhận môi trường của cell đánh giá; cần rà lại tính thống nhất của output train trước khi chốt notebook. |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md):

| Tiêu chí | Trạng thái và bằng chứng |
|---|---|
| Hoàn thành và định dạng YOLO | Có 50 file, checker đạt; 689 box sau rework thuộc bốn ID quy định. |
| Đầy đủ vật thể | Đã gán thủ công bộ nhãn ban đầu và có một vòng rework; FN giảm từ 305 xuống 213, vẫn cần phân biệt nguyên nhân bằng ảnh và ánh xạ lớp. |
| Hình học box | Mean IoU của matches sau test lại là 0.8630; vẫn phải rà những box không khớp, không suy ra toàn bộ box đều sát. |
| Phân lớp nhất quán | Có guideline và schema cố định; còn bất đồng cần xác nhận giữa nhãn học viên và gold. |
| Mini guideline | Đã có định nghĩa lớp, quy tắc box và 16 ca mơ hồ có tên ảnh, quyết định, lý do. |
| Gold-set QC | Đã rework và chạy lại với gold gốc; F1 0.6557, `passed=false`. Cần tạo lại file JSON đầu ra hiện đang thiếu trước khi nộp. |
| Pipeline YOLO và phân tích | Có log 50 epoch, metrics, 10 ảnh prediction và nhận xét cụ thể; cấu hình giữ split. Điều kiện annotation trước train chưa được đáp ứng theo JSON hiện tại. |

Các việc còn cần hoàn thiện: xác nhận vấn đề gold và mốc khóa nhãn với giảng viên; tiếp tục xử lý các box chưa khớp sau lần test lại; xác nhận lượt self-QC sau xuất nhãn và rà sự thống nhất giữa output train/eval trong notebook. Tên GPU cùng phiên bản Python, Ultralytics và Torch của cell đánh giá đã được xác nhận ở mục 5. Khi chốt bài cần tạo lại và thêm riêng `outputs/annotation_evaluation.json` vì file hiện đang thiếu và `outputs/` bị Git bỏ qua, kiểm tra link Colab hiển thị được cho người chấm, push bài và nộp link trên LMS. Báo cáo chưa xác nhận đã nộp LMS hoặc đã đạt toàn bộ các checkpoint.

## 8. Công cụ AI và tài liệu tham khảo

Các công cụ đã sử dụng:

- **CVAT local trên Docker:** tự vẽ bounding box và chọn lớp cho 50 ảnh train.
- **Google Colab / Ultralytics YOLO:** chạy notebook, train, validation và xuất ảnh dự đoán.
- **ChatGPT/Codex:** hỗ trợ đọc yêu cầu, giải thích định dạng và lỗi; chạy kiểm tra cấu trúc, đối chiếu số liệu/ảnh; hỗ trợ vòng rework bằng việc bổ sung/chỉnh box và lớp, nhập bản sửa vào CVAT; hỗ trợ soạn câu chữ trong mini guideline và báo cáo. Nội dung và nhãn được AI hỗ trợ cần được học viên rà lại và xác nhận; không coi các nhận xét chưa được giảng viên xác nhận là kết luận chính thức.
- **Phần xác nhận tự thực hiện:** gán nhãn thủ công và quan sát toàn bộ 50 ảnh ở bộ nhãn ban đầu, chạy notebook Colab, xuất lại nhãn sau rework và chạy lại evaluator với gold gốc. Kết quả test lại đã được cung cấp và ghi ở mục 4.
