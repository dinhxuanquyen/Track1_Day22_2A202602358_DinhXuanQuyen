# AI Critique - Day 22

Ngày 08/10/2026. Hai prompt §4.7 được áp dụng trực tiếp trong phiên phân tích này cho mô hình SmartCheck-in. Các quyết định bên dưới được diễn đạt lại bằng tiếng Việt để người học rà soát. AI hỗ trợ kiểm tra và soạn câu chữ; chưa ghi nhận việc người học đã đọc hiểu hoặc xác nhận toàn bộ quyết định.

## Lượt 1 - Cost/Job Stress Test (§4.7.1)

### Input provided in English

Product: web kiosk for hotel guests with an existing booking. One completed job requires successful identity checks, CHECKED_IN and room_assigned confirmed by the PMS, without staff intervention. No physical key delivery is included.

Model per hotel per month: N=1200 eligible cases, c=85%, R=1020. Target cohort=10 hotels. Google Vision OCR: 2 images x $0.0015. AWS Face APIs: 2 DetectFaces +1 CompareFaces x $0.001. Liveness: 1 x $0.015. Extra full-chain retry rate=8%. Hosting=$60/cohort/month; support=$6/hotel/month; PMS amortization=$2.40/hotel/month; storage/network=$0.002/case; logging/eval=$0.002/case. Internal QA=3% x 2 minutes x $4/hour. Customer handles escalation (variant A). Allocated R&D/sales=$30/hotel/month. Hybrid revenue=$30+$0.11 N, cap 1200; no added charge for retry. All operational inputs are estimates, not eval results.

### Prompt applied

Act as a ruthless CFO and a skeptical infrastructure engineer. Review this Cost/Job model without rewriting the assumptions. Identify missing costs, check the completed-job denominator, recompute API arithmetic, assess caching/batch applicability and price volatility, solve the 60% margin boundary, show sensitivity at 50%,60%,70%,80%,90% containment, and identify the single input that breaks the model if wrong by 2 x.

### Findings and arithmetic

1. Cost coverage: API, retry, hosting/DB, network/storage, observability/eval, QA, support, connector and overhead are included. Hardware, payment/key issuance and document authenticity/database verification are excluded by proposed scope, not assumed free. Integration scope and storage retention can invalidate the budgets. $6 support means only 1.5 hours/month; bespoke hotel integrations need a new quote. Onboarding $80 cost is separated from recurring costs.

2. Denominator: COGS $51.216/1020=$0.05021176/completed. Dividing by attempts gives $0.04268:15% understatement relative to correct cost; correct completed-job cost is 17.65% higher than the attempted-job figure. Overhead included cost=$81.216/1020=$0.07962353.

3. API math: OCR $0.003 +Face $0.003 +liveness $0.015=$0.021/attempt. Retry=$0.00168/attempt. Monthly API $25.20,retry $2.016. No LLM means token caching saves $0. Batch is not used for real-time check-in.

4. Price risk: published non-promotional rates are used without free-tier credits. Region, contract and services have not been selected. All API/retry prices doubling raises COGS to $78.432 and reduces GM to 51.585%. Supplier change cannot be recommended as a real saving without comparable identity-security eval.

5. Hybrid margin: at fixed N and HITL A, containment does not affect the monthly revenue or COGS. The actual 60% GM condition is N>=181 at cohort 10. Value-based containment condition is c>=81% at N 1200. A single financial containment threshold from an Outcome template would be misleading.

| c | Completed | Cost/completed USD | Hybrid GM | Fixed-price Outcome reference GM |
|---|---:|---:|---:|---:|
|50%|600|0.085360|68.385%|46.255%|
|60%|720|0.071133|68.385%|55.212%|
|70%|840|0.060971|68.385%|61.610%|
|80%|960|0.053350|68.385%|66.409%|
|90%|1080|0.047422|68.385%|70.142%|

Outcome reference fixes price at $0.15882353/completed job. c_min=0.04268/(0.15882353 x 0.4)=67.1815%. This is not the selected invoice model.

6. Two critical failure modes: API price 2 x loses the 60% margin target. Worse, if the provider rather than the hotel must handle failed jobs, escalation costs 180 x 10/60 x $4=$120/month, raising COGS to $171.216; GM=-5.689%. Do not sell managed results at the proposed Hybrid price.

### Quyết định sau phản biện chi phí

| Ý kiến phản biện | Quyết định | Cách tôi xử lý trong bài |
|---|---|---|
| Tính mọi chi phí AI bằng token | Reject | Bài này dùng OCR, đối chiếu mặt và liveness. Tôi tính theo ảnh/lần kiểm tra, không dùng chi phí chatbot để thay thế. |
| Cần tính cả gọi lại API và kiểm tra của con người | Accept | Tôi giữ chi phí retry và QA riêng. Hai khoản này là dự phòng có giải thích, chưa phải số đo thật. |
| Chia chi phí cho số hồ sơ đã thử | Reject | Tôi chia cho 1,020 job hoàn thành trong kịch bản 1,200 hồ sơ và containment 85%. Như vậy chi phí một job không bị tính thấp đi. |
| Dùng ngưỡng containment của Outcome cho Hybrid | Reject | Tôi giữ 67.18% làm số tham chiếu cho Outcome. Với Hybrid, ngưỡng GM 60% là ít nhất 181 hồ sơ/tháng ở cohort 10; mức 81% bảo vệ giá trị cho khách. |
| Xem chi phí ở 10 hotel là chi phí pilot | Reject | 10 hotel là kịch bản mục tiêu. Nếu chỉ có 2 hotel ở cùng volume, GM còn khoảng 53.57%; tôi ghi rõ khác biệt này. |
| Nhà cung cấp phải mua thiết bị cho mọi hotel | Partial | Trong phạm vi hiện tại, hotel tự có kiosk/camera. Nếu tôi phải cung cấp thiết bị thì cần tính lại chi phí và báo giá. |
| OCR có độ tin cậy cao là đủ để triển khai an toàn | Reject | Tôi vẫn cần đo nhận nhầm, từ chối nhầm và kiểm tra giả mạo. Chưa có eval nên chưa kết luận sản phẩm an toàn. |
| Nhà cung cấp xử lý miễn phí mọi hồ sơ lỗi | Reject | Tôi chọn HITL A: hotel xử lý hồ sơ chuyển lễ tân; nhà cung cấp vẫn trả QA và hỗ trợ. Nếu đổi trách nhiệm, giá hiện tại có thể làm GM âm. |

## Lượt 2 - Channel Reality Check (§4.7.3)

### Input provided in English

ARPU=$162/month; GM=68.385%; SMB payback allowance=12 months; computed CAC budget=$1329.408. Chosen channel=founder-led Sales-Led. Pain moment: 14:00-16:00, an arriving guest waits for document entry and room assignment at reception while the hotel works in its PMS. Product surface=web kiosk in the lobby with a PMS check-in API. No pilot hotel, PMS supplier or partner is confirmed. Proposed quota=$60000/year;250 working days; loaded AE cost=$10000/year. Founder CPO=$80; win rate=25%, neither measured.

### Prompt applied

I am choosing one go-to-market channel for the next 90 days. Recompute deals per AE per year/day and sales viability, compare my CAC budget to realistic opportunity costs, challenge the pain moment and integration surface, state what would falsify this plan quickly, and give the strongest argument against my selected channel. State assumptions and do not invent published benchmarks.

### Findings and arithmetic

ACV=162 x 12=$1944. Quota/ACV=60000/1944=30.864 deals/year; divided by 250=0.12346 deals/day. Arithmetic alone does not establish demand or rep productivity.

Founder CPO=$24 prospecting+$16 demo+$20 travel+$20 tools=$80/opportunity. CAC=80/0.25=$320,0.2407 xallowed budget. Payback=320/(162-51.216)=2.89 months. This uses target-cohort gross profit, not actual pilot gross profit; do not present it as measured payback.

At the stated target unit economics, CPO must stay under 1329.408 x 0.25=$332.352 or win rate above 80/1329.408=6.018%. Sales cycles, churn and support remain unmeasured.

If hiring an AE at $10000/year and achieving quota, salary alone costs 10000/30.864=$324/customer before travel/marketing. That is a separate motion from the founder CAC model, and not a justification to hire now. No universal US benchmark is asserted for founder sales in Vietnam.

The pain moment names time, activity and location; the PMS vendor and access permissions are still missing. The kiosk solves the user's moment, while the sale reaches the hotel's owner/GM; these are different people. Interview both.

The strongest objection is that 1200 monthly cases,10 minutes saved and buyer willingness-to-pay are unverified, while connector/setup/support could exceed the fee. Affordable acquisition cannot rescue a product without measurable labor value.

Fast falsification in the first 2 weeks: complete 10 interviews; verify workflow/buyer/budget and one usable PMS integration path. If no hotel accepts a scoped pilot with disclosed usage and data handling, do not spend on scalable acquisition. The contact/opportunity/paid targets are goals, not evidence already observed.

### Quyết định sau phản biện kênh bán

| Ý kiến phản biện | Quyết định | Cách tôi xử lý trong bài |
|---|---|---|
| Chọn nhiều kênh để dễ xoay chuyển | Reject | Tôi tập trung Sales-Led trong 90 ngày để đủ thời gian tìm hiểu khách và xử lý tích hợp PMS. |
| Ghi Partner-Led dù chưa có đối tác cụ thể | Reject | Tôi chưa có đối tác xác nhận nên không coi đây là kênh đã sẵn sàng. |
| Lấy quota doanh nghiệp Mỹ làm chuẩn | Reject | Tôi dùng quota $60,000/năm như một giả định tính toán và ghi rõ đây không phải bằng chứng năng suất bán hàng. |
| Lấy CAC từ một công ty khác | Reject | Tôi tính CAC từ chi phí cho một cơ hội bán hàng $80 và tỷ lệ thắng giả định 25%, ra $320. |
| Coi CAC $320 là kết quả đã đo | Reject | Tôi ghi rõ CAC là ước tính; cần đo lại từ số khách tiếp cận, cơ hội đủ điều kiện và khách trả phí thật. |
| Tuyển sales ngay vì số deal/ngày nhỏ hơn 1 | Reject | Tôi trực tiếp bán trước để biết khách có nhu cầu, trả tiền và tiếp tục sử dụng hay không. |
| Gắn sản phẩm vào đúng lúc khách cần | Accept | Tôi đặt kiosk ở sảnh, nối PMS để xử lý check-in. PMS cụ thể và quyền API vẫn phải xác nhận. |
| Ghi 5 khách trả phí và 2 pilot như đã đạt | Reject | Đây là mục tiêu kế hoạch. Tôi chưa có khách/pilot được xác nhận để báo là kết quả. |

### Diễn giải các lựa chọn chính để rà soát

Tôi chọn khách sạn làm bên trả tiền vì sản phẩm hỗ trợ công việc của lễ tân. Người sử dụng kiosk là khách lưu trú; người duyệt mua dự kiến là chủ khách sạn hoặc Tổng quản lý. Đây là giả thuyết cần hỏi lại khách sạn.

Tôi chỉ tính một job hoàn thành khi xác thực đạt và PMS xác nhận CHECKED_IN cùng room_assigned mà không cần nhân viên can thiệp. Chụp ảnh thành công chưa đủ để tính là đã giải quyết xong việc check-in.

Tôi chọn Hybrid: $30/hotel/tháng và $0.11/hồ sơ đủ điều kiện bắt đầu OCR, tối đa 1,200 hồ sơ/tháng. Phí nền trả cho dashboard, kết nối PMS, giám sát và hỗ trợ. Ca đã xử lý API nhưng không hoàn thành vẫn tính usage; retry và duplicate không tính thêm. Quy tắc này cần nói rõ với khách trước khi sử dụng.

Tôi chọn giá $162/tháng ở kịch bản 1,200 hồ sơ vì mức đó nằm trong khoảng giá dự kiến $153.648-$170. GM 68.39% là kết quả tính từ giả định, không phải lợi nhuận đã đạt ngoài thực tế. Chi phí có cả overhead là khoảng $0.079624/job; GM chỉ trừ COGS, không đồng nghĩa với lợi nhuận ròng.

Tôi tập trung Sales-Led vì hotel cần trao đổi về quy trình lễ tân, thiết bị và PMS trước khi thử nghiệm. Ngân sách CAC là $1,329.41, còn CAC dự kiến là $320; hai số này giúp kiểm tra kế hoạch nhưng chưa chứng minh tôi bán được hàng.

Tôi chưa coi mức containment 85% hay 10 phút tiết kiệm/job là kết quả thực tế. Tôi cần eval, pilot và số liệu trước/sau để biết có giữ được giá trị cho khách và biên lợi nhuận dự kiến hay không.

## Các việc cần người học xác nhận

Người đọc được chỉ định cho test người lạ là Đỗ Trường Thành Ân - 2A202602899; người học đã xác nhận nội dung cung cấp là phản hồi thực tế, đạt đủ3 ý và hỏi lại2 câu. Chưa có khảo sát ngân sách, willingness-to-pay, dữ liệu eval, khách trả phí, hoặc báo cáo pilot. Hai phản biện trên kiểm tra lập luận và mô hình, không thay thế các bằng chứng thực nghiệm đó.

## Bổ sung điều kiện gãy dưới GM 50%

Giữ N=1,200, cohort 10 và HITL A: GM=1−(24+27.216k)/162. Với k là hệ số giá API/retry, ngưỡng GM 50% là k=(81−24)/27.216=2.094356; vượt khoảng 2.0944 lần sẽ xuống dưới 50%. Đã thêm công thức tại 2_Pricing!H53 và đối chiếu PDF/Word. Chấp nhận bổ sung vì mô tả API tăng 2 lần chỉ chứng minh mất mục tiêu 60%, chưa trả lời đầy đủ câu hỏi GM dưới 50% của mẫu.

Bản diễn đạt lại ở trên là nội dung đề xuất để người học rà soát; chưa ghi nhận xác nhận đọc hiểu. Biên bản test người lạ được lưu riêng trong Test_Nguoi_La.md.


## Bổ sung sau rà soát - 08/10/2026

| Ý kiến | Quyết định | Xử lý |
|---|---|---|
| 181 hồ sơ đủ mọi điều kiện kinh tế của lab | Reject | Đây chỉ là GM60%. Bổ sung công thức R>=3COGS, ngưỡng735 ở tab2 H59:H60. |
| Dùng67.18% như ngưỡng Hybrid | Reject | Giữ con số như Outcome tham chiếu; giải thích riêng trong BoSung_DoiChieu_Lab.md vì One-Pager giữ nguyên. |
| Nêu PMS chung chung | Accept | Chọn Mews Connector API làm ứng viên, có nguồn và luồng đề xuất; chưa có quyền tích hợp hoặc khách xác nhận. |
| Ghi kịch bản AI như test người lạ thật | Reject | Người học đã xác nhận test và phản hồi thật; cập nhật tab5 đủ3 ý, hỏi lại2 câu và kết luận đạt. |
| Ghi85% như eval đã chạy | Reject | Chuẩn bị protocol/CSV và ô đối chiếu; chưa có số đo thì không đánh dấu đạt. |
| Benchmark Chekin thiếu kỳ thanh toán | Accept | Thêm điều kiện thanh toán năm trong tab3/tab6 và tài liệu bổ sung. |

Đây là kết quả rà soát tài liệu và số học, không phải thử nghiệm sản phẩm, không thay xác nhận đọc hiểu của người học.


## Cập nhật kết quả test người lạ

Test thực hiện lúc **12:10 ngày08/10/2026**, người đọc **Đỗ Trường Thành Ân - 2A202602899**. Người học xác nhận ba câu trả lời và hai câu hỏi thêm là phản hồi thực tế. Theo quy trình cung cấp, người đọc xem tài liệu trong2 phút. Kết luận **đạt**: đủ3 ý và hỏi lại2 câu. Biên bản đầy đủ ở Test_Nguoi_La.md; tab5 B29:B31=Được, B32=2.

Word/PDF One-Pager giữ nguyên theo yêu cầu; trạng thái test chưa có kết quả trong bản đó là thông tin của bản trước cập nhật. Biên bản test và tab5 là thông tin mới nhất. Người học chưa cung cấp góp ý riêng ngoài hai câu hỏi; không tự bổ sung lời nhận xét của người đọc. Eval/pilot và quyền/chi phí PMS vẫn chưa có bằng chứng mới.
