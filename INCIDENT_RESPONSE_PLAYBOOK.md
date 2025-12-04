# INCIDENT PLAYBOOK: REDIS CACHE FAILURE

**Mã sự cố:** INC-001
**Mức độ nghiêm trọng:** Critical (P1)
**Mô tả:** Dịch vụ Redis bị gián đoạn, khiến ứng dụng Vote không thể ghi nhận lượt bình chọn của người dùng.

## 1. Dấu hiệu nhận biết (Detection)
- **Monitoring:** Grafana báo container `example-voting-app-redis-1` trạng thái Down (CPU/RAM về 0).
- **User:** Người dùng bấm "Vote" nhưng trang web bị treo hoặc không nhảy số kết quả.

## 2. Quy trình Chẩn đoán (Investigation)
1. SSH vào Server Production.
2. Kiểm tra trạng thái container:
  ```bash
  docker ps | grep redis
  ```
Xem log lỗi của ứng dụng Vote (để xác nhận lỗi kết nối):

```Bash
docker logs example-voting-app-vote-1
```

3. Quy trình Khắc phục (Mitigation)
Khởi động lại dịch vụ Redis ngay lập tức:
```Bash
docker start example-voting-app-redis-1
```
Nếu không lên, thực hiện Force Re-create:

```Bash
docker-compose up -d --force-recreate redis
```

4. Xác nhận (Verification)
Kiểm tra lại lệnh docker ps.
Truy cập trang Result, thử vote một lần nữa xem số có nhảy không.
---

### BƯỚC 2: THỰC HÀNH "CHAOS ENGINEERING" (TỰ TAY ĐẬP PHÁ)

Bây giờ bạn sẽ đóng vai "kẻ phá hoại" để tạo ra sự cố, sau đó đóng vai "người hùng" để cứu nó. Nhớ **CHỤP ẢNH** lại từng bước nhé!

**1. Gây án (Simulate Failure):**
SSH vào server, chạy lệnh "giết" Redis:
```bash
docker stop example-voting-app-redis-1
```
2. Quan sát hậu quả (Evidence):

Vào Grafana: Bạn sẽ thấy đường line của Redis cắm đầu xuống đất (mất tín hiệu). -> Chụp ảnh 1.

Vào Web Vote: Bấm thử bình chọn -> Có thể nó sẽ đơ hoặc lỗi.

3. Thực hiện cứu hộ (Execute Playbook): Chạy lệnh cứu chữa:

```Bash
docker start example-voting-app-redis-1
```

4. Kiểm tra phục hồi: Vào lại Web Vote -> Bấm bình chọn -> Thấy trang Result nhảy số tanh tách -> Hệ thống đã sống lại!
