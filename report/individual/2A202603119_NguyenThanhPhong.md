# Báo cáo thực hành Day 3 — điền trực tiếp trong fork của bạn

- Mã học viên theo lớp: 2A202603119
- Ngày / CVAT local: 11/11/2026 / CVAT local http://localhost:8080
- Công cụ đã dùng: Brush, Polygon trong CVAT; tool auto_label.py (Mask2Former) tạo bản nháp

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task            | File ZIP đúng tên   | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --------------- | ------------------- | -----------------: | ---------------------------: |
| easy_semantic   | easy_semantic.zip   |              3 / 3 |                           20 |
| medium_instance | medium_instance.zip |              3 / 3 |                           32 |
| hard_panoptic   | hard_panoptic.zip   |              2 / 2 |                           30 |
| cp1_holes       | cp1_holes.zip       |              1 / 1 |                            3 |
| cp2_slice       | cp2_slice.zip       |              1 / 1 |                            3 |
| cp5_occlusion   | cp5_occlusion.zip   |              1 / 1 |                            3 |
| cp3_thin        | cp3_thin.zip        |              1 / 1 |                            3 |
| cp4_curb        | cp4_curb.zip        |              1 / 1 |                            3 |
| cp6_coverage    | cp6_coverage.zip    |              1 / 1 |                            3 |
| **Tổng tối đa** |                     |                    |                      **100** |

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: 000000181542.jpg, xe bus ở góc trên bên trái.
- Class và quy tắc tôi dùng để chọn biên: Class: bus. Dùng Polygon vẽ bám theo phần thân xe buýt nhìn thấy ở góc trên-trái ảnh. Biên dừng ở mép lốp xe phía dưới và mép thân xe phía phải — không kéo sang vùng người đứng cạnh xe vì người thuộc class person riêng. Phần đầu xe chạm mép trái ảnh (x=1) nên tôi kéo polygon sát mép ảnh, không để hở. Quy tắc: "chỉ vẽ phần nhìn thấy, không đoán phần bị che".
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Sau khi tự vẽ bus xong, tôi dùng bản nháp từ auto_label.py cho các vật còn lại. Tôi phát hiện tool bỏ sót xe tải (truck) hoàn toàn — không có mask nào trong Objects panel. Tôi vẽ tay thêm truck bằng Polygon. Lý do giữ/sửa: truck là thing phải có instance riêng theo quy tắc, không thể để thiếu.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: medium_instance / 000000181542.jpg / xe tải (truck) giữa ảnh

- Lỗi thuộc loại: thiếu vật (FN)

- Bằng chứng tôi nhìn thấy: sau khi chạy scorer lần đầu, kết quả hiện
  "submitted 54 vs GT 71, FN=32" — kiểm lại trong CVAT thấy xe tải (192×131px
  tại tọa độ x=273, y=114) chưa được vẽ mask.

- Quy tắc và hành động sửa: theo quy tắc instance, truck là "thing" phải có
  mask riêng. Tôi dùng Polygon vẽ bao quanh thân xe, bám theo phần nhìn thấy,
  không kéo vào vùng xe khác che.

- Sau sửa đã Save và export lại chưa? Có — Save trong CVAT, export lại
  COCO 1.0, ghi đè submissions/medium_instance.zip.

- Kết quả tự đánh giá sau sửa: R@0.5 tăng từ 0.55 → 0.72, metric từ 0.420 → 0.543,
  điểm từ 1.4 → 10.2 / 32. Tổng 3 tier: 35.4 / 82.
  Mục 4 — Ba ca chưa chắc hoặc đã cân nhắc

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí                                                              | Hai cách hiểu có thể                                                                                                     | Quy tắc/chứng cứ                                                                                                                     | Quyết định hoặc câu hỏi                                                               |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------- |
| medium / 000000181542.jpg / motorcycle rộng bên trái (x=11,y=256,w=350) | (1) Vẽ toàn bộ cả cụm xe máy + người ngồi thành 1 instance. (2) Tách phần xe và phần người thành 2 instance riêng.       | Quy tắc: motorcycle là class cần vẽ; người ngồi trên xe không phải class riêng trong task này nên gộp vào mask xe.                   | Chọn cách 1 — vẽ toàn bộ xe máy kể cả người ngồi thành 1 instance class motorcycle.   |
| medium / 000000373353.jpg / người đứng sát xe ở y≈303                   | (1) Người bị xe che một phần vẫn là 1 instance đầy đủ. (2) Chỉ vẽ phần nhìn thấy, không đoán phần bị che.                | Quy tắc: "chỉ vẽ phần nhìn thấy, không tự đoán biên sau vật che".                                                                    | Chọn cách 2 — chỉ vẽ phần người thực sự nhìn thấy trong ảnh.                          |
| hard_panoptic / 000000460147.jpg / sidewalk                             | (1) Vùng lề đường bên phải là sidewalk vì có bó vỉa phân cách. (2) Vùng đó là phần mở rộng của road vì cùng màu asphalt. | Quy tắc curb: "ranh road–sidewalk theo chức năng/bó vỉa, không chỉ màu ảnh". Scorer hiện sidewalk FP=2, TP=0 → tôi đang vẽ sai vùng. | Câu hỏi cho coach: vùng nào trong ảnh 460147 được tính là sidewalk theo ground truth? |
