# Điểm nhúng và PMS dự kiến

Ngày 08/10/2026. Người phụ trách: Đinh Xuân Quyền. Lựa chọn phục vụ kế hoạch lab, chưa là tích hợp đã chạy.

## Lựa chọn và căn cứ

PMS dự kiến: **Mews**, dùng **Mews Connector API**. Chọn làm ứng viên vì có tài liệu công khai về đặt phòng, check-in và môi trường thử nghiệm. Chưa có bằng chứng hotel mục tiêu tại Việt Nam đang dùng Mews, chưa có quan hệ đối tác và chưa có quyền API production. Mews là nơi tích hợp; kênh bán vẫn là founder Sales-Led.

Theo tài liệu chính thức, `reservations/getAll/2023-06-06` đọc booking, `reservations/start` chuyển booking sang `Started` (check-in). API start đòi hỏi booking `Confirmed`, thời điểm phù hợp và phòng đã gán, đã kiểm tra. Trường `AssignedResourceId` xác nhận phòng. Request cần `ClientToken` và `AccessToken`. Mews có Demo và Production; production cần certification và token cho property.

Nguồn kiểm tra 08/10/2026:

- https://docs.mews.com/connector-api/operations/reservations
- https://docs.mews.com/connector-api/guidelines/authentication
- https://docs.mews.com/connector-api/guidelines/environments

## Luồng tích hợp đề xuất, chưa triển khai

1. Khách mở web kiosk `/check-in/booking` ở sảnh, nhập/quét mã booking. Backend tìm booking trong phạm vi đúng hotel.
2. Kiểm tra điều kiện booking và dữ liệu tối thiểu. Chỉ bắt đầu ledger usage khi gọi OCR; retry không tạo case mới.
3. Chạy OCR, face match, liveness. Kết quả không chắc, không có phòng sẵn sàng hoặc dịch vụ lỗi thì chuyển lễ tân.
4. Sau khi eKYC đạt, backend gọi start và đọc lại booking. Chỉ ghi event nội bộ `CHECKED_IN`/`room_assigned` khi PMS xác nhận `Started` và `AssignedResourceId` khác rỗng.
5. Timeout phải đối soát trạng thái trước khi gửi lại. Khóa chống trùng nội bộ: hotel_id + booking_id + arrival_id. Không giả định PMS có cơ chế idempotency tương đương nếu chưa kiểm chứng.

Phạm vi pilot ban đầu: hotel chuẩn bị/gán phòng trước phiên kiosk, không cần nhân viên can thiệp giữa phiên. Kiosk xác minh phòng được gán và hoàn tất check-in; chưa tuyên bố đã tự tối ưu/gán phòng mới hoặc cấp chìa khóa. Công chuẩn bị phòng phải được tính khi đo tiết kiệm lao động; không ghi 10 phút tiết kiệm là kết quả đã chứng minh.

## Việc cần xác minh và tiêu chí dừng

| Việc                  | Bằng chứng cần thu                                                                      | Owner           | Hạn mục tiêu | Trạng thái             |
| --------------------- | --------------------------------------------------------------------------------------- | --------------- | ------------ | ---------------------- |
| Tìm hotel phù hợp     | Phỏng vấn 10 buyer/lễ tân, ghi PMS đang dùng; tìm 2 hotel chấp thuận pilot              | Đinh Xuân Quyền | 22/10/2026   | Chưa thực hiện         |
| Quyền Demo/Production | Xác nhận đăng ký, certification, token theo hotel; không đưa token vào repo             | Đinh Xuân Quyền | 22/10/2026   | Chưa đăng ký/liên hệ   |
| Luồng kỹ thuật        | Booking hợp lệ/sai, phòng chưa sẵn sàng, timeout, đọc lại, retry/duplicate              | Đinh Xuân Quyền | 29/10/2026   | Chưa gọi API           |
| Chi phí connector     | Báo giá API/PMS, hỗ trợ và certification; đối chiếu $2.40/hotel/tháng và onboarding $80 | Đinh Xuân Quyền | 22/10/2026   | Chưa có báo giá        |
| Pilot                 | Chấp thuận hotel, dữ liệu và fallback; tên owner phía hotel                             | Đinh Xuân Quyền | Trước pilot  | Chưa có hotel xác nhận |

Nếu không tiếp cận được hotel dùng Mews, quyền API hoặc chi phí không phù hợp, chưa chạy pilot; chọn lại PMS từ phỏng vấn và tính lại connector/onboarding. Không coi việc có tài liệu công khai là bằng chứng đã được phép tích hợp.
