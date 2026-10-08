# Bổ sung đối chiếu Day22

Ngày 08/10/2026. One-Pager Word/PDF được giữ nguyên theo yêu cầu người học; tài liệu này bổ sung cách hiểu các con số, không thay thế bản đó.

## Các ngưỡng của Hybrid/HITL A

Giữ cohort 10, phí nền $30, usage $0.11, chi phí cố định F=$14.4/hotel/tháng, chi phí biến đổi v=$0.03068/hồ sơ:

R(N)=30+0.11N; COGS(N)=14.4+0.03068N.

| Điều kiện | Phép tính | Kết quả | Excel |
|---|---|---|---|
| GM >=60% | COGS <=0.4R | N>=180.180180, làm tròn lên **181** | 2_Pricing!H22 |
| Giá >=3 x Cost/Job | R >=3COGS | N>=734.966592, làm tròn lên **735** | 2_Pricing!H59 |
| Đồng thời đạt hai điều kiện lab | MAX(181,735) | **735 hồ sơ/tháng** | 2_Pricing!H60 |
| Phí <=25% giá trị lao động, N=1200 | 162 <=0.25 x1200 xc x10/60 x4 | c>=**81%** | 2_Pricing!H24 |

Giải hệ số giá: 30+0.11N >=43.2+0.09204N, suy ra 0.01796N>=13.2. Ở N=734, R=$110.74 và 3COGS=$110.75736, chưa đạt; ở N=735, R=$110.85 và 3COGS=$110.8494, đạt. Ở N=181, GM đạt khoảng60.02%, nhưng R/COGS chỉ khoảng2.50 lần.

Các ngưỡng volume trên chưa bảo đảm giá trị khách. Khi N giảm, phí nền chiếm tỷ trọng lớn hơn; phải tính lại H24 theo N và đối chiếu thời gian tiết kiệm thật. Tại N=1200, ngưỡng ROI là81%, giả định85% còn dư4 điểm phần trăm. Không gọi phần dư này là bằng chứng an toàn thống kê.

Tại N=1200, c=85%, doanh thu $162, COGS $51.216; R/COGS=3.163074; Cost/Job COGS=$0.05021176, có overhead=$0.07962353; GM=68.385185%. Hệ số giá3x trong mẫu dùng Cost/Job COGS; overhead$30 được trình bày riêng, không bị bỏ quên.

Dòng67.18% trong One-Pager chỉ là tham chiếu mô hình Outcome giá/job cố định. Dòng181 là số hồ sơ cho GM60%, không là tỷ lệ containment và không bảo đảm điều kiện3x. Hybrid/HITL A ở N cố định không có ngưỡng containment tài chính riêng trong mô hình hiện tại. Containment vẫn tác động giá trị, nhu cầu và retention; giả định N/chi phí cố định không phải dự báo chúng luôn độc lập trong thực tế.

## Bằng chứng và các mục cần thực hiện thật

- Containment85% là ước lượng kế hoạch chưa chạy eval. CSV kết quả được chuẩn bị nhưng chưa có dòng dữ liệu; không coi protocol là Eval Results.
- Tab5 H20:H23 nhận số auto-PASS, số case đủ điều kiện và tính containment, so với ngưỡng ROI. Ô thiếu dữ liệu hiển thị chưa đo/chưa đủ dữ liệu.
- Mews Connector API là PMS dự kiến có tài liệu; chưa có quyền tích hợp, khách hay báo giá. Chi tiết trong PMS_Integration.md.
- Test người lạ đã thực hiện lúc12:10 ngày08/10/2026; người học xác nhận phản hồi thật. Test_Nguoi_La.md ghi đủ3 ý,2 câu hỏi lại và kết luận đạt.
- Eval, Procurement Q&A và Pilot Report giữ deadline mục tiêu; deadline không có nghĩa công việc đã hoàn tất.
- Hai lượt AI critique và bảng accept/reject đã có; người học cần đọc hiểu trước khi defend bài.

## Điều kiện benchmark Chekin

Mức từ€3.95/property/tháng gắn với thanh toán năm ở cấu hình vacation rental; không coi là giá hotel/kiosk đầy đủ. Đã cập nhật tab3 và tab6; One-Pager giữ nguyên nên đọc benchmark kèm điều kiện này. Nguồn kiểm tra08/10/2026: https://chekin.com/en/pricing/

## Việc cuối trước khi nộp

Test người lạ đã cập nhật theo phản hồi thật người học cung cấp; nếu có eval thì cập nhật số đo cùng dataset/version. Kiểm tra lại file và nộp link repo trên Vlearn. Chưa push, chưa submit hay liên hệ bên ngoài trong lần cập nhật này.


## Cập nhật kết quả test người lạ

Test thực hiện lúc **12:10 ngày08/10/2026**, người đọc **Đỗ Trường Thành Ân - 2A202602899**. Người học xác nhận ba câu trả lời và hai câu hỏi thêm là phản hồi thực tế. Theo quy trình cung cấp, người đọc xem tài liệu trong2 phút. Kết luận **đạt**: đủ3 ý và hỏi lại2 câu. Biên bản đầy đủ ở Test_Nguoi_La.md; tab5 B29:B31=Được, B32=2.

Word/PDF One-Pager giữ nguyên theo yêu cầu; trạng thái test chưa có kết quả trong bản đó là thông tin của bản trước cập nhật. Biên bản test và tab5 là thông tin mới nhất. Người học chưa cung cấp góp ý riêng ngoài hai câu hỏi; không tự bổ sung lời nhận xét của người đọc. Eval/pilot và quyền/chi phí PMS vẫn chưa có bằng chứng mới.
