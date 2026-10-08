# Day 22 - SmartCheck-in Kiosk

Đinh Xuân Quyền - 2A202602358. Ngày lập và kiểm tra nguồn giá: 08/10/2026.

## Trạng thái và bài nộp

Dự án đang triển khai, chưa có kiểm thử định lượng. Bài này là mô hình kế hoạch có giả định công khai, không phải báo cáo kết quả thật.

Hai tệp chính nằm ở thư mục gốc repo; tài liệu bổ sung nằm trong thư mục docs:

- [DinhXuanQuyen_Day22_model.xlsx](../DinhXuanQuyen_Day22_model.xlsx): 5 tab làm việc và 2 tab hướng dẫn/tham chiếu.
- [DinhXuanQuyen_Day22_onepager.pdf](../DinhXuanQuyen_Day22_onepager.pdf): bản PDF xuất từ Word đã điền theo đúng cấu trúc template gốc; 3 trang, giữ đầy đủ đề mục, bốn bảng và bài test người lạ.
- [DinhXuanQuyen_Day22_filled_template.docx](DinhXuanQuyen_Day22_filled_template.docx): bản Word đã điền đầy đủ theo mẫu gốc, 3 trang để chỉnh sửa và xem chi tiết.
- [Evidence_Pack.md](Evidence_Pack.md): kế hoạch eval, Procurement Q&A và protocol/báo cáo pilot chưa chạy.
- [AI_Critique.md](AI_Critique.md): phản biện theo hai prompt ở §4.7, kết quả và quyết định áp dụng.
- [Test_Nguoi_La.md](Test_Nguoi_La.md): biên bản kết quả thực tế với người đọc Đỗ Trường Thành Ân - 2A202602899; đã thực hiện test với Đỗ Trường Thành Ân lúc12:10 ngày08/10/2026; đủ3 ý, hỏi lại2 câu, đạt theo biên bản người học cung cấp.

Hai mẫu gốc được giữ lại. Theo lựa chọn của người học, bài làm giữ đúng cấu trúc Day22-AI-Product-GTM-One-Pager-Template.docx: đủ đề mục, bốn bảng, Risk Checklist và ba câu test người lạ. Bản Word điền đầy đủ dài 3 trang; PDF nộp bài được xuất từ chính bản Word này. Không dùng bản rút gọn một trang trong bộ nộp. Chưa submit Vlearn, push repo hoặc liên hệ khách hàng.

## Trạm 1 - Ngân sách và Job

Phiên bản A: công cụ web kiosk eKYC hỗ trợ check-in, thường đi vào ngân sách phần mềm.

Phiên bản B được chọn: tự động xử lý thủ tục nhận phòng cho khách đã đặt trước, giảm thời gian chờ và công thao tác lễ tân. Ngân sách dự kiến là vận hành lễ tân; người duyệt dự kiến là chủ khách sạn/Tổng quản lý, phải xác nhận bằng phỏng vấn.

Người dùng là khách lưu trú, bên mua là khách sạn. Job hoàn thành khi eKYC đạt và PMS xác nhận CHECKED_IN cùng room_assigned, không cần nhân viên xử lý giữa chừng. Gán phòng chưa chứng minh khách đã nhận chìa khóa vật lý. Không tính giá trị bằng số lần chụp ảnh.

Khóa chống trùng đề xuất: hotel_id + booking_id + arrival_id xuyên suốt các session. Session_id vẫn dùng phân tích UX, nhưng không đủ để đếm job/lập hóa đơn.

Phạm vi pilot đề xuất: booking đơn, một khách cần eKYC, hotel tự trang bị kiosk/tablet/camera. Nhóm nhiều khách, thanh toán, phát thẻ và xác minh dữ liệu giấy tờ chính thức cần đánh giá và báo giá riêng.

## Trạm 2 - Value Metric

Chọn Hybrid: $30/hotel/tháng + $0.11/hồ sơ đủ điều kiện được xử lý, cap 1,200 hồ sơ/tháng. Phí nền mua dashboard, một PMS connector, giám sát và 1.5 giờ support/tháng. Phí nền không bao gồm lượt miễn phí.

Hồ sơ tính tiền phải có booking hợp lệ, đủ dữ liệu tối thiểu và bắt đầu gọi OCR. Ca thất bại sau xử lý vẫn là usage, quy tắc này phải công khai trước với khách. Sai booking, bỏ dở trước API và duplicate không thu phí. Retry trong cùng case không thu thêm. Đến cap, dừng inference/chuyển lễ tân; tăng quota cần đổi gói. Chưa có hợp đồng áp dụng.

Attribution 3/10, Autonomy 2/10 được chấm thận trọng dựa trên thiết kế, không tự nhận có log/eval. Chưa bán Outcome vì chưa có bằng chứng về attribution và độ tin cậy.

Benchmark [Chekin](https://chekin.com/en/pricing/) dùng subscription theo property/phòng; trang hiển thị từ €3.95/property/tháng theo thanh toán năm, không phải giá một khách sạn/kiosk. [Duve](https://duve.com/pricing/) Basic tối thiểu $120/tháng; automation có phụ phí. Hai sản phẩm dùng để so đơn vị tính tiền, không lấy giá đó làm giá trần.

## Trạm 3 - Cost/Job và giá

Kịch bản cơ sở là unit economics mục tiêu của một hotel trong cohort 10 khách trả phí, không phải hiện trạng:

- 120 phòng × occupancy 80% × 30 ngày / 2 đêm = 1,440 lượt đến tiềm năng.
- Giả định 1,200 hồ sơ đủ điều kiện vào kiosk, tương ứng 83.33% lượt đến.
- Containment giả định 85% → 1,020 hoàn thành, 180 phiên chưa xong.
- Retry dự phòng 8% hồ sơ chạy lại một vòng eKYC đầy đủ, chưa phải số đo.
- QA 3% hồ sơ × 2 phút × $4/giờ = $4.80/tháng.
- HITL A: lễ tân khách sạn xử lý escalation; nhà cung cấp vẫn trả QA/support.
- Hosting/DB $60/tháng chia 10 hotel; support $6/hotel/tháng; connector $2.40/hotel/tháng.
- Storage/network $0.002 và logging/eval $0.002 mỗi hồ sơ thử.
- Overhead R&D/Sales phân bổ $30/hotel/tháng, tách khỏi Gross Margin.
- 26,000 VND/USD là tỷ giá kế hoạch, không phải tỷ giá giao dịch hiện hành.

API eKYC tham chiếu: OCR 2 ảnh × $0.0015; Face API gồm 2 DetectFaces +1 CompareFaces × $0.001; liveness 1 check × $0.015. Tổng $0.021/hồ sơ thử; retry $0.00168. Không trừ free tier hay giá khuyến mại.

Google Vision hỗ trợ tiếng Việt, nhưng không tự chứng minh đọc đúng CCCD. Giá AWS tham chiếu Mỹ, chưa quyết định region, nhà cung cấp hoặc nơi triển khai thực tế.

| Thành phần | USD/hotel/tháng | Ô Excel |
|---|---:|---|
| API eKYC | 25.200 | 1_Cost_Job!H28 |
| Retry eKYC | 2.016 | 1_Cost_Job!H29 |
| Infra gồm support/connector | 19.200 | 1_Cost_Job!H30 |
| QA nội bộ | 4.800 | 1_Cost_Job!H31 |
| COGS | 51.216 | 1_Cost_Job!H32 |
| Overhead | 30.000 | 1_Cost_Job!H33 |
| Tổng có overhead | 81.216 | 1_Cost_Job!H34 |

Cost/Job COGS = 51.216/1,020 = $0.050212. Có overhead = $0.079624. Giá sàn quy đổi 3 × Cost/Job = $0.150635; giá quy đổi Hybrid = 162/1,020 = $0.158824. Giá quy đổi chỉ để so unit economics, không là đơn vị xuất hóa đơn.

GM=(162−51.216)/162=68.385%. Lãi gộp $110.784; sau overhead phân bổ còn $80.784, chưa phải lợi nhuận ròng công ty.

Neo giá: giả định 10 phút công giải phóng/auto-PASS ở $4/giờ → 1,020 × 10/60 × 4=$680/tháng. Trần 25%=$170; sàn doanh thu 3 × COGS=$153.648; chọn $162 nằm trong vùng giá. Đây là giá trị thời gian, không được hứa cắt lương khi chưa chứng minh giảm biên chế. Nếu thời gian tiết kiệm thấp hơn, vùng giá có thể biến mất.

Onboarding đề xuất $100 một lần, COGS dự kiến $80 (10 giờ× $4 +$20 cấu hình +$20 kiểm tra), GM20%; tách khỏi ARPU/GM định kỳ.

### Phân biệt ngưỡng Hybrid và Outcome

Hybrid/HITL A, giữ N và cohort cố định:

Revenue = 30 + 0.11 N

COGS = 14.4 + 0.03068 N

Containment c thay đổi Cost/Job=COGS/(Nc), nhưng không đổi doanh thu/COGS vì khách trả theo hồ sơ xử lý và hotel chịu escalation. Vì vậy không có ngưỡng containment tài chính riêng cho mô hình này.

Để GM ≥ 60%:

14.4 + 0.03068 N ≤ 0.4(30 +0.11 N) → N ≥ 180.18018 → cần 181 hồ sơ/tháng ở cohort 10.

Để giá bán ≤ 25% giá trị thời gian ở N=1,200:

162 ≤ 0.25 × 1,200 × c× 10/60 × 4 → c ≥ 81%.

81% là ngưỡng bảo vệ giá trị cho khách, không phải ngưỡng GM.

Bảng Outcome gốc giữ để tham chiếu nếu sau này bán theo kết quả với giá cố định $0.158824/job: ngưỡng GM60%=67.1815%, ngưỡng GM50%=53.7452%. Không dùng hai số này tuyên bố Hybrid hòa vốn theo containment. Bảng Hybrid đúng nằm tại 2_Pricing!G43:J49.

| Tình huống | GM dự kiến | Kết luận |
|---|---:|---|
| Cohort 10, N=1,200, c=85% | 68.39% | Mục tiêu chưa kiểm chứng |
| Cohort 2, vẫn N=1,200/hotel | 53.57% | Chưa đạt 60%; pilot volume thấp còn kém |
| Cohort 5, N=1,200 | 64.68% | Có thể đạt nếu đủ volume |
| Toàn bộ giá API/retry tăng 2 × | 51.59% | Mất biên mục tiêu |
| Nhà cung cấp chịu escalation, biến thể B | -5.69% | Không bán managed outcome ở giá này |
| Containment 60%, giữ Hybrid A | 68.39% | GM không đổi, ROI khách kém |

Tại N=1,200/hotel cần cohort ≥ 4 để GM ≥ 60%. Ngân sách học pilot đề xuất $500, chưa chi. Không nới ngưỡng xác thực để làm đẹp containment.

## Trạm 4 - Kênh

Một kênh trong 90 ngày: Sales-Led do Đinh Xuân Quyền trực tiếp discovery, demo và chốt pilot trong một ngách dùng chung PMS. Chưa có partner/khách sạn xác nhận.

ARPU=$162; GM68.385%; payback allowance 12 tháng theo quy ước SMB của lab:

CAC budget=162 × 68.385% × 12=$1,329.408.

CPO giả định $80=$24 tìm hiểu +$16 demo +$20 đi lại +$20 công cụ. Win rate 25% → CAC $320=0.241 × ngân sách, payback 2.89 tháng. CPO tối đa theo ngân sách $332.352; win rate tối thiểu khoảng 6.02%. Chưa phải CAC đo được.

ACV=$1,944; quota giả định $60,000/năm → 30.864 deal/năm /250 ngày =0.123 deal/ngày. Kiểm tra số học chưa chứng minh bán được. Loaded AE giả định $10,000/năm → $324/deal riêng lương nếu đạt quota; chưa tuyển AE, không cộng trùng vào CAC founder.

PLG khó khi cần cấu hình PMS/kiosk; Partner-Led chưa có quan hệ. Founder Sales-Led giúp học nhu cầu, nhưng phải đo sales cycle, support, churn trước khi mở rộng. Không dùng CPO của doanh nghiệp Mỹ thay chi phí founder ở Việt Nam. Điểm channel scorecard là đánh giá chủ quan theo giả định, không phải kết quả nghiên cứu thị trường.

## Trạm 5 - Pain Moment và 90 ngày

Giả thuyết: 14:00-16:00, khách có booking chờ nhập giấy tờ và gán phòng tại quầy/PMS. Web kiosk ở ngay sảnh, nối PMS check-in API, không yêu cầu cài app. PMS dự kiến là Mews Connector API; cần xác minh hotel mục tiêu, quyền và chi phí qua discovery, hiện chưa có quyền tích hợp.

Kế hoạch 09/10/2026-06/01/2027:

- Tháng 1:10 interviews; chọn một PMS; eval 200 hợp lệ+100 lỗi/tấn công; tìm 2 hotel chấp thuận pilot.
- Tháng 2-3:200 prospect → 20 qualified opportunities → mục tiêu 5 khách trả phí lũy kế. Pilot ≥ 200 phiên/hotel; đo công lao động, latency, containment, retry, usage ledger.
- Tháng 4+: mục tiêu cohort 10; chỉ mở cùng PMS khi GM thật ≥ 60% hai tháng, eval an toàn đạt và có số retention.

Đinh Xuân Quyền chịu trách nhiệm, owner phía hotel chưa xác nhận. Containment 85% và p95≤ 3 phút là mục tiêu.

## Trạm 6 - Evidence và kiểm tra trước nộp

Evidence_Pack.md có kế hoạch, Q&A chuẩn bị và protocol pilot. Không tạo Eval Results/Pilot Report giả.

- Eval: deadline 22/10/2026, chưa chạy.
- Procurement Q&A: có bản chuẩn bị; cấu hình và câu trả lời cần xác nhận trước 29/10/2026.
- Pilot Report: có protocol, chưa có hotel; deadline mục tiêu 06/01/2027.
- Test người lạ: đã ghi người đọc Đỗ Trường Thành Ân - 2A202602899; đã thực hiện test với Đỗ Trường Thành Ân lúc12:10 ngày08/10/2026; đủ3 ý, hỏi lại2 câu, đạt theo biên bản người học cung cấp.

Đã kiểm tra số học, thay đổi input, render và mở lại Excel bằng bộ đọc độc lập. Đây là kiểm tra file, không phải kiểm thử sản phẩm. Công thức xám mẫu được giữ tương đương; shared formulas được khai triển để cache xuất file được tính lại đúng. Khối G:J thêm trong cùng các tab để biểu diễn eKYC/Hybrid, không giả chi phí eKYC thành token chat.

Ở containment=0, Cost/Job hoàn thành không xác định; các công thức gốc có trả 0 để chống chia 0 không được hiểu là chi phí bằng 0. Mô hình khuyến nghị không dùng để kết luận khi không có job hoàn thành.

Các quyết định đã được diễn đạt lại bằng tiếng Việt trong AI_Critique.md và One-Pager để người học rà soát. Chưa ghi nhận xác nhận đọc hiểu; người học cần xác nhận các lựa chọn trước khi nộp theo hướng dẫn lab. Vẫn cần phỏng vấn, báo giá region thực và pilot.

## Nguồn

Kiểm tra 08/10/2026:

- https://cloud.google.com/vision/pricing
- https://docs.cloud.google.com/vision/docs/languages
- https://aws.amazon.com/rekognition/pricing/
- https://docs.aws.amazon.com/rekognition/latest/APIReference/API_CompareFaces.html
- https://chekin.com/en/pricing/
- https://duve.com/pricing/
- HD_Lab22.md: rubric, hệ số 3 ×, GM ≥ 60%, quy ước CAC và yêu cầu nộp; không xác nhận các giá mẫu lịch sử khác.

## Nộp bài

Repo theo hướng dẫn: Track1_Day22_2A202602358_DinhXuanQuyen. Nộp link repo lên Vlearn trước buổi tiếp theo. Chưa có lịch buổi tiếp theo để ghi ngày nộp cụ thể.

## Đối chiếu checklist lab

| Yêu cầu | Trạng thái và nơi xem |
|---|---|
| 5 nhóm chi phí, mẫu số job hoàn thành | Đã điền tab 1; eKYC được tách theo ảnh/check ở G:J |
| Giá, GM và độ nhạy | Đã tính tab 2; 68.4% là kịch bản mục tiêu, không phải GM pilot |
| Breakeven containment đối chiếu eval | Có ngưỡng Outcome tham chiếu và ngưỡng Hybrid và ngưỡng 735 hồ sơ cho điều kiện 3x; chưa thể đối chiếu số đo vì chưa có eval |
| Value metric, Decision Note, 2 benchmark | Đã điền tab 3 và tab 6, có nguồn và ngày kiểm tra |
| CAC, deal/AE/ngày, một kênh | Đã tính tab 4; chọn founder Sales-Led, số acquisition là giả định |
| 90 ngày, owner, Evidence deadline | Đã điền tab 5 và Evidence_Pack.md |
| One-Pager 3 khối, số khớp Excel | Word/PDF 3 trang giữ đầy đủ cấu trúc template gốc, các số đã đối chiếu Excel |
| Hai prompt và accept/reject | Có trong AI_Critique.md; cần người học đọc hiểu các quyết định |
| Test người lạ | Đỗ Trường Thành Ân - 2A202602899; đã thực hiện test với Đỗ Trường Thành Ân lúc12:10 ngày08/10/2026; đủ3 ý, hỏi lại2 câu, đạt theo biên bản người học cung cấp |
| Nộp link repo | Người học thực hiện trên Vlearn trước buổi tiếp theo |

## Ngưỡng GM 50% của Hybrid

Ở N=1,200, cohort 10 và HITL A, COGS ngoài API/retry = $24/tháng, API/retry cơ sở = $27.216/tháng. Gọi k là hệ số tăng giá API/retry:

GM = 1 − (24 + 27.216k)/162.

GM = 50% khi k = (81 − 24)/27.216 = 2.094356. Vượt khoảng 2.0944 lần thì GM dưới 50% (2_Pricing!H53). Nếu chỉ tăng support, ngưỡng tương ứng là $35.784/hotel/tháng (H54). Giữ nguyên các đầu vào khác khi sử dụng hai ngưỡng này.

## Mở Excel trong VS Code

Bản Excel cuối đã được Microsoft Excel mở, tính lại và lưu thành XLSX tương thích. Đã kiểm tra bằng chính parser Office Viewer 4.2.0: bản cuối nhận đủ 7 tab và nội dung, mọi giá trị/công thức giữ nguyên. Nếu tab Office Viewer đang mở vẫn hiển thị Sheet1 trống, đóng tab đó rồi mở lại DinhXuanQuyen_Day22_model.xlsx để tải bản đã sửa. Bộ ZIP đã cập nhật cùng bản này.


## Cập nhật sau rà soát 08/10/2026

Đã bổ sung công thức **735 hồ sơ/tháng** tại 2_Pricing!H59:H60 để đồng thời đạt GM>=60% và giá>=3xCost/Job; 181 chỉ là ngưỡng GM60%. Kịch bản 1,200 hồ sơ đạt cả hai. Các ngưỡng giữ cohort10 và HITL A.

- [Bổ sung đối chiếu](BoSung_DoiChieu_Lab.md): phép tính và cách đọc cùng One-Pager giữ nguyên.
- [PMS dự kiến](PMS_Integration.md): Mews Connector API, đường tích hợp và trạng thái quyền/chi phí chưa xác nhận.
- [Protocol eval](Eval_Protocol.csv): phân bổ200 ca hợp lệ và100 ca lỗi/tấn công, chưa chạy.
- [Phiếu dữ liệu eval](Eval_Results.csv): chỉ có tiêu đề, chờ kết quả thật. Không phải báo cáo eval.

Tab5 H20:H23 có ô nhập kết quả thật, công thức containment và đối chiếu ngưỡng ROI. Chưa có số đo thì hiển thị chưa đo/chưa đủ dữ liệu. Khi đánh giá containment vận hành, báo riêng tập hợp lệ đại diện và tập lỗi/tấn công; không lấy tỷ lệ pha trộn nhân tạo200/100 làm tỷ lệ khách thực tế.

Người học xác nhận đã test lúc12:10 ngày08/10/2026; đạt đủ3 ý với2 câu hỏi lại. Biên bản đã cập nhật kết quả thực tế do người học cung cấp. Word/PDF One-Pager giữ nguyên theo yêu cầu; các giải thích mới nằm trong bản bổ sung. Mức Chekin từ€3.95/property/tháng cần đọc kèm điều kiện thanh toán năm.


## Cập nhật kết quả test người lạ

Test thực hiện lúc **12:10 ngày08/10/2026**, người đọc **Đỗ Trường Thành Ân - 2A202602899**. Người học xác nhận ba câu trả lời và hai câu hỏi thêm là phản hồi thực tế. Theo quy trình cung cấp, người đọc xem tài liệu trong2 phút. Kết luận **đạt**: đủ3 ý và hỏi lại2 câu. Biên bản đầy đủ ở [Test_Nguoi_La.md](Test_Nguoi_La.md); tab5 B29:B31=Được, B32=2.

Word/PDF One-Pager giữ nguyên theo yêu cầu; trạng thái test chưa có kết quả trong bản đó là thông tin của bản trước cập nhật. Biên bản test và tab5 là thông tin mới nhất. Người học chưa cung cấp góp ý riêng ngoài hai câu hỏi; không tự bổ sung lời nhận xét của người đọc. Eval/pilot và quyền/chi phí PMS vẫn chưa có bằng chứng mới.
