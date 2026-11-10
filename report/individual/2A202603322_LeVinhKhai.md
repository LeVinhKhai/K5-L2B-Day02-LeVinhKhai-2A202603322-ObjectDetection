# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Lê Vĩnh Khải
- MSSV: 2A202603322
- Email: 26ai.khailv@vinuni.edu.vn
- Lớp: 2B LAB E403 
- Link repo bài làm: https://github.com/LeVinhKhai/K5-L2B-Day02-LeVinhKhai-2A202603322-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1lSxzl9FqVO7QzesxooyFphkpTvOOcl7Y?usp=sharing

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.
Bộ dữ liệu sử dụng bốn lớp phương tiện gồm car, truck, bus và van. Tôi phân biệt các lớp dựa trên đặc điểm hình thái và mục đích sử dụng của phương tiện:

car: xe con 4–9 chỗ, bao gồm sedan, SUV, hatchback, MPV và xe bán tải; thường nhỏ hơn van, không có khoang hàng lớn như truck và không có cấu trúc thân dài, nhiều chỗ ngồi như bus.

truck: xe tải thùng, xe đầu kéo hoặc xe kéo rơ-moóc; đặc trưng bởi cabin và khoang chở hàng phía sau, thường có kích thước và khả năng chở hàng lớn hơn van.

bus: xe buýt hoặc xe khách có thân xe dài, nhiều chỗ ngồi và nhiều cửa sổ; kích thước và cấu trúc thân xe thường lớn hơn rõ rệt so với van.

van: xe thân hộp, dùng để chở người hoặc hàng, có cabin và khoang phía sau liền khối; thường lớn hơn car nhưng nhỏ hơn truck, đồng thời không có số lượng cửa sổ và chỗ ngồi nhiều như bus.

Quy tắc gán bounding box

Bounding box được vẽ bám sát phần phương tiện có thể quan sát được, hạn chế lấy dư nền và không tính phần bóng đổ vào box. Với phương tiện bị che khuất, tôi vẫn gán nhãn nếu phần còn nhìn thấy cung cấp đủ thông tin để xác định lớp. Tương tự, phương tiện bị cắt bởi rìa ảnh vẫn được annotate nếu còn đủ đặc trưng nhận dạng và box chỉ bao quanh phần vật thể thực sự xuất hiện trong khung hình.

Đối với phương tiện ở quá xa, quá nhỏ hoặc quá mờ, tôi chỉ gán nhãn khi vẫn có thể xác định chắc chắn nó thuộc một trong bốn lớp. Nếu thông tin thị giác không đủ để phân lớp một cách đáng tin cậy, tôi không tự suy đoán class nhằm hạn chế tạo nhãn nhiễu.

Ba trường hợp mơ hồ tiêu biểu

drive_006 — phương tiện bị cắt khung và ảnh mờ: chỉ quan sát được một phần đầu xe, không đủ đặc trưng của thân xe để phân biệt chắc chắn giữa car và van. Quyết định: không gán nhãn, do vật thể vừa bị cắt khung vừa thiếu thông tin cần thiết để xác định class.
Cần làm rõ: Ngưỡng tối thiểu bao nhiêu phần của phương tiện phải nhìn thấy thì vẫn được phép gán nhãn? Nếu chỉ thấy đầu xe nhưng không thấy thân xe thì có được annotate không?

drive_007 — phương tiện bị cây che khuất: một phần xe bị che, hình ảnh mờ và không nhìn rõ cấu trúc thân xe nên khó phân biệt car với van. Quyết định: không gán nhãn, vì phần nhìn thấy chưa đủ để xác định lớp một cách chắc chắn và việc gán sẽ mang tính suy đoán.
Cần làm rõ: Với vật thể bị che khuất, cần nhìn thấy bao nhiêu đặc trưng hoặc bao nhiêu phần trăm thân xe thì được coi là đủ để xác định class?

drive_017 — phương tiện ở rất xa: một số xe có kích thước rất nhỏ và hình ảnh mờ, không thể xác định chắc chắn chúng thuộc car, van, truck hay bus. Quyết định: không gán các phương tiện quá xa/mờ, chỉ annotate những vật thể còn đủ thông tin thị giác để xác định một trong bốn lớp.
Cần làm rõ: Có quy định kích thước tối thiểu của object hoặc mức độ rõ tối thiểu để annotate không? Ví dụ object nhỏ hơn X pixel hoặc không phân biệt chắc class thì có bỏ qua không?
Các quyết định trên tuân theo nguyên tắc chung là ưu tiên tính nhất quán và độ tin cậy của nhãn: chỉ annotate khi có đủ bằng chứng trực quan để xác định lớp thay vì cố gắng gán nhãn cho mọi vật thể xuất hiện trong ảnh.

## 3. Annotation và self-QC

- Số file nhãn train: ĐẠT cấu trúc: 50/50 file nhãn hợp lệ
- Kết quả checker và lỗi đã xử lý: Checker xác nhận đủ số file, đúng basename, cấu trúc mỗi dòng hợp lệ và tọa độ YOLO nằm trong phạm vi cho phép. Tuy nhiên, kết quả này chỉ kiểm tra tính hợp lệ về cấu trúc, không thay thế việc kiểm tra trực quan chất lượng bounding box và class.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: drive_001, drive_002, drive_003, drive_004, drive_006, drive_007, drive_008, drive_011, drive_017, drive_019
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:
Phát hiện một số phương tiện ở rất xa hoặc quá mờ đã được gán nhãn dù không đủ đặc trưng để xác định class → loại bỏ các box không đáp ứng tiêu chí nhận dạng đã đặt ra trong GUIDELINE_MINI.md.

Phát hiện trường hợp bỏ sót phương tiện có thể nhận diện được → bổ sung box và kiểm tra lại class.

Kiểm tra các trường hợp dễ nhầm giữa car, van, truck và bus; những trường hợp không đủ thông tin thị giác được giữ theo nguyên tắc không suy đoán class.

Rà lại độ bám sát của bounding box, tránh box quá rộng, lấy dư nền hoặc gộp nhiều vật thể vào cùng một box.
   
- Commit khóa nhãn ban đầu: `f31a835` -`Fix bus label naming and refresh annotations and update GUIDELINE\_MINI` — phiên bản annotation cuối cùng sau self-QC, trước khi nhận/xem gold.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 448 | |
| FP | 455 | |
| FN | 205 | |
| Precision | 0.4961 | |
| Recall | 0.6861 | |
| F1 | 0.5758 | |
| mean_iou_of_matches | 0.8384 | |
| passed | passed khi lab_coach chỉnh ngưỡng F1>0.5 | |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. 
Kết quả evaluator: [`outputs/annotation_evaluation.json`](../../outputs/annotation_evaluation.json)
Không thực hiện rework thêm sau khi đối chiếu gold, vì Lab Coach đã cập nhật ngưỡng đạt thành F1 > 0.50 do gold dataset có một số annotation chưa nhất quán. Kết quả ban đầu của tôi đã đạt ngưỡng F1 = 0.5758 và mean IoU = 0.8384.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab, GPU T4, Ultralytics: 8.4.208, PyTorch: 2.14.1+cu130, CUDA available: True
- Các tham số thay đổi so với notebook nếu có và lý do: Không thay đổi; giữ nguyên cấu hình mặc định của notebook
- Đường dẫn best checkpoint trong phiên chạy: /content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4105453698519763 |
| Recall | 0.23026854853628678 |
| mAP50 | 0.27225886498171903 |
| mAP50–95 | 0.20168300309748077 |

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
Trong kết quả đánh giá trên 10 ảnh eval, lớp bus có recall thấp nhất với giá trị 0.0000. Các lớp còn lại có recall lần lượt là car = 0.638, truck = 0.200 và van = 0.0833.
Ảnh minh họa: drive_053.jpg. Ảnh này có nhiều phương tiện dạng bus, trong đó có một xe bus lớn ở phần dưới ảnh, nên phù hợp để kiểm tra trực quan khả năng nhận diện của model đối với lớp này. 
Ảnh minh họa: [`drive_053.jpg`](assets/eval_predictions/visuals/drive_053.jpg).
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
Nếu mAP50 cao nhưng mAP50–95 thấp, model có thể đã phát hiện đúng vị trí tương đối của object ở ngưỡng IoU thấp, nhưng bounding box chưa đủ chính xác khi yêu cầu IoU cao hơn. Khi đó cần kiểm tra chất lượng định vị của box, đặc biệt là các trường hợp box bị lệch, quá rộng, quá hẹp, lấy dư nhiều nền hoặc chưa bám sát biên của vật thể.
Trong kết quả của tôi, mAP50 = 0.2723 và mAP50–95 = 0.2017. Vì mAP50–95 thấp hơn mAP50, cần kiểm tra thêm độ chính xác hình học của các bounding box trên tập eval, không chỉ khả năng phát hiện đúng class.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
Các class dễ gây nhầm lẫn nhất về mặt guideline là van, truck và một số trường hợp giữa car và van, do các đặc trưng phân biệt như cấu trúc thân xe, khoang hàng và số lượng cửa sổ trở nên khó quan sát khi vật thể ở xa, nhỏ, mờ hoặc bị che khuất.

Kết quả eval cũng cho thấy recall của van chỉ đạt khoảng 0.0833 và truck đạt 0.200, thấp hơn đáng kể so với car = 0.638. Điều này phù hợp với những khó khăn đã ghi trong GUIDELINE_MINI.md, đặc biệt ở các trường hợp vật thể ở xa, bị che hoặc không nhìn rõ cấu trúc thân xe.

Tuy nhiên, các chỉ số recall chỉ cho thấy model gặp khó khăn với những lớp này; để kết luận một lỗi cụ thể là do guideline cần kết hợp kiểm tra trực quan prediction và annotation, không chỉ suy luận từ metric.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
Không dùng 10 ảnh eval để train vì cần giữ chúng tách khỏi dữ liệu huấn luyện. Nếu đưa các ảnh này vào train rồi vẫn dùng chính chúng để đánh giá, model đã được cập nhật trọng số từ dữ liệu đánh giá, dẫn tới data leakage/evaluation contamination. Khi đó các metric có thể bị thiên lệch và không còn phản ánh đúng khả năng tổng quát hóa của model trên dữ liệu chưa thấy.

Trong notebook này, 10 ảnh eval được dùng làm validation set thông qua data.yaml. Chúng được sử dụng trong quá trình train để theo dõi chất lượng model và lựa chọn checkpoint best.pt. Sau đó, best.pt được đánh giá trên chính tập eval để lấy các chỉ số Precision, Recall, mAP50 và mAP50–95.

Vì tập eval đã tham gia vào quá trình validation và lựa chọn best.pt, nó không phải test set độc lập. Vai trò của tập này là hỗ trợ đánh giá và lựa chọn model trong quá trình huấn luyện, chứ không được dùng để trực tiếp cập nhật trọng số như tập train.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Giai đoạn annotation trên CVAT | Một số phương tiện bị che khuất, ở xa hoặc ảnh mờ nên khó phân biệt giữa car, van, truck, bus |Xây dựng GUIDELINE_MINI.md, đặt quy tắc chỉ gán nhãn khi còn đủ đặc trưng để xác định class; các trường hợp quá mơ hồ thì không suy đoán | Giảm việc gán class tùy ý và tạo được nguyên tắc xử lý nhất quán cho các ca khó |
|Đối chiếu với gold set | Gold dataset có một số trường hợp phân lớp chưa nhất quán, đặc biệt giữa van và truck, làm F1 thấp hơn ngưỡng ban đầu trong script |Chạy evaluate_annotations.py, kiểm tra trực quan một số ảnh và đối chiếu với thông báo của Lab Coach |F1 đạt 0.5758, mean IoU đạt 0.8384. Lab Coach cập nhật ngưỡng đạt thành F1 > 0.50 do hạn chế của gold set, nên không tiếp tục chỉnh nhãn chỉ để khớp gold |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.

| Tiêu chí | Trạng thái tự kiểm tra | Bằng chứng |
|---|---|---|
| Hoàn thành và định dạng YOLO | Đã hoàn thành | Có đủ 50 file nhãn trong `dataset/labels/train/`, cùng basename với ảnh train; checker xác nhận `50/50` file hợp lệ, mỗi dòng đúng cấu trúc YOLO và tọa độ normalized |
| Đầy đủ vật thể | Đã thực hiện self-QC | Đã rà trực quan nhiều ảnh train, bổ sung một số object bị bỏ sót và xử lý các trường hợp xe bị che/cắt khung theo `GUIDELINE_MINI.md` |
| Độ bám sát và hình học box | Đạt ở các box đã match | Gold-set evaluation cho `mean_iou_of_matches = 0.8384`, cao hơn ngưỡng `0.70`; quá trình self-QC cũng đã rà lại box quá rộng, lấy dư nền hoặc gộp nhiều vật thể |
| Phân lớp nhất quán | Đã kiểm tra và chuẩn hóa | Sử dụng bốn class `car`, `truck`, `bus`, `van` và kiểm tra các trường hợp dễ nhầm giữa các class |
| Mini guideline và quyết định mơ hồ | Đã hoàn thành | `GUIDELINE_MINI.md` có định nghĩa bốn lớp, quy tắc bounding box và nhiều trường hợp mơ hồ có quyết định cùng lý do; report trình bày ba case tiêu biểu |
| Gold-set annotation QC | Đạt theo tiêu chí cập nhật của Lab Coach | Kết quả: `F1 = 0.5758`, `mean IoU = 0.8384`. Script gốc vẫn dùng ngưỡng F1 `0.80`, nhưng Lab Coach đã cập nhật ngưỡng chấp nhận thành `F1 > 0.50` do gold set có một số annotation chưa nhất quán |
| Pipeline YOLO và phân tích | Đã hoàn thành | Notebook chạy thành công trên Google Colab với NVIDIA Tesla T4; train `yolo26n.pt`, đánh giá trên 10 ảnh eval, lưu Precision/Recall/mAP, prediction images, `results.csv`, confusion matrix và các biểu đồ đánh giá |
| Không dùng eval để train | Đã tuân thủ | 10 ảnh `eval` chỉ được sử dụng làm validation/evaluation theo `data.yaml`, không đưa vào tập train |
| Cấu trúc file nộp | Đã chuẩn bị | Đã đặt `eval_predictions/` và `yolo26_runs/` trong `report/individual/assets/`; báo cáo có link tương đối tới ảnh dự đoán và kết quả evaluator |

### Điều kiện bắt buộc

- Có đủ 50 file nhãn train và checker chạy hợp lệ.
- Có gold-set evaluation tại [`outputs/annotation_evaluation.json`](../../outputs/annotation_evaluation.json).
- Không đưa `gold_train_labels/` vào repo nộp bài.
- Không dùng 10 ảnh `eval` để train.
- Không thay đổi gold labels hoặc split dữ liệu.
- Model có mAP thấp nhưng đây không phải điều kiện tự động trừ điểm theo rubric; phần báo cáo đã phân tích hạn chế của dữ liệu, class imbalance và chất lượng annotation.

Nhìn chung, các thành phần bắt buộc của pipeline đã được thực hiện và có bằng chứng lưu trong repo. Phần chấm điểm cuối cùng, đặc biệt đối với chất lượng annotation và tiêu chí gold-set sau khi Lab Coach cập nhật ngưỡng F1, thuộc đánh giá của giảng viên/Lab Coach.
## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.
 Trong quá trình thực hiện bài, tôi có sử dụng một số công cụ hỗ trợ để kiểm tra thao tác, giải thích lỗi và rà lại nội dung báo cáo:

- **ChatGPT**: hỗ trợ giải thích một số khái niệm, cách đọc kết quả evaluation, hướng dẫn xử lý lỗi khi thao tác với Git, CVAT và Google Colab, đồng thời hỗ trợ chỉnh cách diễn đạt, rà soát cách trình bày báo cáo.
- **Google Colab**: dùng để chạy notebook huấn luyện và đánh giá model YOLO26 trên GPU NVIDIA Tesla T4.
- **CVAT**: dùng để gán nhãn, kiểm tra trực quan bounding box và đối chiếu annotation.
- **Git/GitHub**: dùng để quản lý phiên bản annotation, lưu commit khóa nhãn và nộp repository bài làm.

Các quyết định annotation, việc lựa chọn class, bounding box, xử lý các ca mơ hồ và kiểm tra kết quả cuối cùng đều được tôi tự thực hiện và đối chiếu lại với `GUIDELINE_MINI.md`, rubric và output thực tế của notebook.

### Tài liệu tham khảo

- Tài liệu và notebook được cung cấp trong repository của bài lab.
- `GUIDE.md`, `RUBRIC.md`, `RULES.md`, `SUBMISSION.md` trong repository.
