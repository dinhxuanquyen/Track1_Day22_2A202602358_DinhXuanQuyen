# Evidence Pack - SmartCheck-in Kiosk

Trạng thái 08/10/2026: chưa kiểm thử, chưa có khách sạn/pilot xác nhận. Tài liệu này là kế hoạch kiểm chứng và câu trả lời chuẩn bị, không là chứng nhận triển khai. Đinh Xuân Quyền phụ trách.

## 1. Eval Results - kế hoạch và mẫu kết quả

Deadline 22/10/2026. Chưa có dữ liệu kết quả.

### Phạm vi và dữ liệu

Chuẩn bị 200 ca hợp lệ, phân tầng CCCD/passport, ánh sáng, góc mặt, thiết bị và độ tuổi; thêm 100 ca lỗi/tấn công: sai người, ảnh in, video phát lại, giấy tờ mờ/thiếu mặt, booking sai, timeout/PMS lỗi. Chỉ sử dụng dữ liệu có quyền dùng; ưu tiên dữ liệu thử nghiệm được phép trong giai đoạn xây bộ eval.

Liveness, face match và OCR không tự chứng minh giấy tờ thật hoặc khớp cơ sở dữ liệu chính thức. Ca ngoài phạm vi/không chắc chuyển lễ tân. Không tự hạ ngưỡng để tăng containment.

Tách tập để hiệu chỉnh ngưỡng khỏi tập đánh giá cuối. Lưu version code/model, ngưỡng, cấu hình camera, ngày chạy và nhãn do người kiểm tra xác nhận. Nếu 300 ca chưa đủ đại diện, ghi rõ giới hạn và bổ sung trước sử dụng thật.

### Event và metric

case_id=hotel_id+booking_id+arrival_id. session_id phục vụ UX. Dùng cùng case_id cho retry, QA, kết quả và ledger tính phí.

| Event/field đề xuất | Khi ghi | Metric |
|---|---|---|
| session_started | Người dùng bắt đầu nhập booking | Funnel UX, chưa phải billable usage |
| eligible_case_started | Booking hợp lệ và bắt đầu gọi OCR | N kinh tế, ledger usage |
| live_face_captured | Ảnh được chụp và gửi server thật | First capture success |
| ekyc_decision | Có kết quả OCR/face/liveness và reason code | False accept/reject, OCR exact match |
| retry_api_completed | Mỗi extra API có số tiền/đơn vị tính | Retry cost ratio |
| escalated | Chuyển nhân viên, có lý do | HITL escalation |
| room_assigned | PMS xác nhận CHECKED_IN và gán phòng | Activation, NSM |
| case_closed | Kết thúc hoặc timeout theo protocol | Bỏ dở, latency, outcome |

Containment=số case auto-PASS/số case đủ điều kiện đã bắt đầu. Tính cả bỏ dở sau started_at và ca PMS lỗi trong mẫu số. Không dùng session retry làm nhiều case.

NSM=số arrival hoàn tất tự động/tháng, không phải tỷ lệ.
Activation=room_assigned trong ≤ 3 phút từ session_started.
STP=số case hoàn tất không retry/số case bắt đầu; auto-PASS có retry vẫn là containment nhưng không STP.
Retry cost=chi phí API bổ sung/chi phí API vòng đầu;8% là giả định extra full-chain, không thay trực tiếp bằng tỷ lệ chụp lại.
Ghi p50/p95 latency và phút công của nhân viên trước/sau, không dùng thời gian khách đứng trước máy thay thời gian lao động.
False acceptance=số ca không hợp lệ được auto-PASS/số ca không hợp lệ đã test. False rejection=số ca hợp lệ bị từ chối/số ca hợp lệ đã test. Containment chịu thêm UX/PMS, không đồng nhất với 1−false rejection.
OCR confidence>90% chỉ là tín hiệu của model; đo thêm exact match các trường quan trọng bằng nhãn thật.

### Gate dự kiến

- Containment mục tiêu ≥ 85%, p95≤ 3 phút; chưa đạt phải phân tích lý do.
- Không auto-PASS các ca attack đã biết trong tập eval trước pilot.
- Báo Wilson 95% confidence interval cho containment. Không khẳng định tỷ lệ lỗi hiếm bằng mẫu nhỏ hoặc nói 0 false accept có nghĩa tuyệt đối an toàn.
- Kiểm tra F5, mở session mới, timeout/retry, submit đồng thời không tạo thêm room_assigned hoặc hóa đơn cùng arrival.
- Nếu xác thực không chắc/PMS chưa xác nhận, không tự gán thành công.

### Mẫu báo cáo chưa điền kết quả

| Trường | Trạng thái |
|---|---|
| Dataset/version, thời điểm chạy | Chưa có |
| Ca hợp lệ / không hợp lệ | Kế hoạch 200/100, chưa chạy |
| Containment, khoảng tin cậy | Chưa đo |
| False accept/reject theo nhóm | Chưa đo |
| OCR exact match, first capture | Chưa đo |
| Latency p50/p95, retry cost | Chưa đo |
| Chi phí cloud và QA thực | Chưa đo |
| Quyết định release/khắc phục | Chưa thể kết luận |

## 2. Procurement Q&A - bản chuẩn bị cần xác nhận

Deadline 29/10/2026. Chưa có triển khai/cấu hình/DPA để bảo đảm các câu trả lời sau. Đây là nội dung cần hoàn tất trước pilot.

### AI có thể nhận nhầm hay bị giả mạo không?

Có rủi ro false accept, false reject, replay và OCR sai. Sản phẩm dự kiến dùng liveness, face match, kiểm tra trường OCR/booking và chuyển nhân viên khi không chắc. Cần gửi eval theo version và nhóm lỗi, ngưỡng quyết định, kill switch và quy trình xử lý sự cố. Không dùng từ “không hallucinate” để né rủi ro xác thực.

Bằng chứng cần có: report eval thật, regression log, ngưỡng được phê duyệt, audit trail, owner nhận escalation. Chưa có.

### Dữ liệu có được dùng train model không?

Chưa thể cam kết khi chưa chọn dịch vụ, tài khoản và điều khoản xử lý dữ liệu. Thiết kế đề xuất không tái sử dụng dữ liệu khách để huấn luyện; điều này cần được xác nhận bằng điều khoản nhà cung cấp, cấu hình opt-out nếu áp dụng, DPA và danh sách subprocessors. Không suy từ CompareFaces stateless rằng toàn bộ pipeline không giữ dữ liệu.

Bằng chứng cần có: điều khoản/data-use từng nhà cung cấp, region, cấu hình lưu trữ, mục đích xử lý, phạm vi truy cập, thông báo/chấp thuận phù hợp. Giá AWS vùng Mỹ hiện chỉ dùng làm tham chiếu chi phí; chưa là quyết định đưa dữ liệu khách sang vùng đó.

### Startup ngừng hoạt động thì dữ liệu ở đâu?

Đề xuất hợp đồng cho hotel quyền xuất dữ liệu cần thiết dạng CSV/JSON, có lịch bàn giao và xác nhận xóa. Không dự kiến duy trì collection khuôn mặt dùng để tìm kiếm. Cần chốt retention cho ảnh/video/result/log từng loại và cách xóa cả bản sao lưu; không tự chọn thời hạn lưu như một kết luận pháp lý.

Bằng chứng cần có: mẫu export, kiểm tra restore/export/delete, sơ đồ dữ liệu, trách nhiệm sở hữu/quản lý và điều khoản chấm dứt. Chưa có.

### Phạm vi trách nhiệm và lỗi vận hành

Hotel tự trang bị thiết bị/mạng và chịu xử lý escalation. Nhà cung cấp chịu cloud, QA và support trong phạm vi gói. Chưa cóSLA 24/7, bảo hiểm, chứng nhận hoặc cam kết tuân thủ nào để tự công bố. PMS downtime không được ghi thành check-in thành công. Có quy trình fallback thủ công và ngừng inference khi lỗi.

### Hóa đơn và tranh chấp

$30 tháng +$0.11/case đủ điều kiện, cap 1200. Không thu retry/duplicate, ca đã xử lý API nhưng thất bại vẫn tính usage và phải được người mua chấp thuận rõ. Xuấtledger gồm case_id, started_at, trạng thái, phí; hạn chế dữ liệu định danh trong ledger. Refund trường hợp tính trùng/lỗi lập hóa đơn. Cơ chế và thời hạn khiếu nại cần ghi trong hợp đồng thực.

## 3. Pilot Report - protocol và mẫu chưa chạy

Deadline báo cáo mục tiêu 06/01/2027. Chưa có tên hotel/owner đồng ý, không ghi “đã ký” hoặc “đã triển khai”.

### Thiết kế

Tìm 2 hotel dùng cùng PMS, có thiết bị/mạng phù hợp. Sau khi hoàn thành eval, quyền dữ liệu, thông báo sử dụng và hợp đồng pilot, chạy ít nhất 200 case/hotel. Đo baseline công lễ tân trên booking tương đương rồi đo kiosk; ghép theo khung giờ/độ phức tạp, ghi rõ giới hạn so sánh, không gọi là A/B randomized nếu không randomize.

Theo dõi containment, STP, retry từng API, bỏ dở, latency, false accept/reject, phút công thực, COGS, support và willingness-to-pay. Dữ liệu kiểm thử và dữ liệu live phải báo riêng.

### Tiêu chí quyết định

- Hoàn tất ≥ 200 case/hotel và báo cáo đủ mẫu số, khoảng tin cậy, lý do exclusion.
- Mục tiêu containment ≥ 85%, p95≤ 3 phút; dừng auto-PASS khi phát hiện false accept nghiêm trọng.
- Đối chiếu giá trị công thực với giả định 10 phút và $4/giờ, không tự tuyên bố giảm biên chế.
- Khách chấp thuận quy tắc usage, giới hạncap, fallback, dữ liệu và chi phí.
- Cohort 2 tạiN 1200/hotel chỉ GM53.57%; pilot volume thấp còn kém. Pilot là chi phí học, không là bằng chứng economics scale.
- Ngân sách học đề xuất $500, chưa chi; acquisition 90 ngày $1600 tách riêng, có cả công founder quy đổi.

### Mẫu báo cáo

| Trường | Nội dung hiện tại |
|---|---|
| Hotel, owner, PMS, thiết bị | Chưa xác nhận |
| Ngày bắt đầu/kết thúc | Chưa chốt |
| Case started/completed/escalated/abandoned | Chưa đo |
| Containment/STP/latency/false accept/reject | Chưa đo |
| Công lao động baseline/kiosk, cách lấy mẫu | Chưa đo |
| API/infra/QA/support/cloud invoice | Chưa đo |
| Giá trị thời gian và phí sẵn lòng trả | Chưa đo |
| Mức hài lòng, vấn đề và quyết định mua | Chưa có phản hồi |
| Quyết định mở rộng và việc cần sửa | Chưa thể kết luận |

## Test người lạ

Người đọc do người học chỉ định: **Đỗ Trường Thành Ân - 2A202602899**. Người học xác nhận test lúc12:10 ngày08/10/2026; đủ3 ý và2 câu hỏi lại. Kết luận đạt theo biên bản người học cung cấp.

Theo quy trình người học cung cấp, người đọc xem One-Pager và Excel trong2 phút. Đã ghi đủ ba câu trả lời và hai câu hỏi lại trong Test_Nguoi_La.md.

Phiếu ghi kết quả thực tế nằm trong [Test_Nguoi_La.md](Test_Nguoi_La.md).


## Bổ sung cách chạy và đối chiếu eval

Dùng Eval_Protocol.csv làm kế hoạch phân bổ300 ca, Eval_Results.csv để thu kết quả từng case. Hiện file kết quả chỉ có tiêu đề; chưa được phép điền mục tiêu85% thành kết quả đã đo. Tập calibration phải riêng với300 ca đánh giá cuối; không dùng tập đánh giá để chỉnh ngưỡng.

Báo containment cho tập hợp lệ đại diện riêng với tập attack/invalid/operational; nếu tập test cố ý trộn200/100, tỷ lệ tổng không đại diện traffic thật. False accept chỉ tính trên các ca nhãn xác thực không hợp lệ thích hợp; PMS timeout không phải ca giả mạo danh tính. Deduplicate case_id; các ca thiếu kết quả phải giải thích hoặc xử lý timeout, không được bỏ khỏi mẫu số để làm đẹp số.

Đếm auto-PASS khi eKYC đạt, không có nhân viên can thiệp trong phiên, PMS xác nhận check-in và có phòng. Điền số hoàn thành/thử thực đo tại tab5 H20:H21 cho cùng tập hợp lệ; H22 tính tỷ lệ, H23 đối chiếu ngưỡng ROI kế hoạch hiện tại. Phải báo thêm số mẫu, dataset/version và khoảng tin cậy. Chưa đủ dữ liệu thì giữ chưa đo.

Gọi n là số case và k là số auto-PASS. Với z=1.96, p=k/n, Wilson95% có tâm (p+z²/(2n))/(1+z²/n), bán kính z×sqrt(p(1-p)/n+z²/(4n²))/(1+z²/n). Khi chưa có k,n thật, không xuất khoảng tin cậy hoặc kết luận đạt.

PMS dự kiến: Mews Connector API, xem PMS_Integration.md. Pilot ban đầu yêu cầu phòng được hotel gán và kiểm tra trước phiên; công chuẩn bị phòng thuộc baseline đo lao động. Chưa có quyền API, hotel xác nhận hay báo giá; chưa thể cam kết $2.40 connector và $80 onboarding đủ chi phí.

Ngưỡng volume bổ sung: tại cohort10, GM60% cần181 hồ sơ/tháng; điều kiện giá>=3xCost/Job cần735. Chưa dùng hai ngưỡng này làm bằng chứng có khách hoặc có demand.


## Cập nhật kết quả test người lạ

Test thực hiện lúc **12:10 ngày08/10/2026**, người đọc **Đỗ Trường Thành Ân - 2A202602899**. Người học xác nhận ba câu trả lời và hai câu hỏi thêm là phản hồi thực tế. Theo quy trình cung cấp, người đọc xem tài liệu trong2 phút. Kết luận **đạt**: đủ3 ý và hỏi lại2 câu. Biên bản đầy đủ ở Test_Nguoi_La.md; tab5 B29:B31=Được, B32=2.

Word/PDF One-Pager giữ nguyên theo yêu cầu; trạng thái test chưa có kết quả trong bản đó là thông tin của bản trước cập nhật. Biên bản test và tab5 là thông tin mới nhất. Người học chưa cung cấp góp ý riêng ngoài hai câu hỏi; không tự bổ sung lời nhận xét của người đọc. Eval/pilot và quyền/chi phí PMS vẫn chưa có bằng chứng mới.
