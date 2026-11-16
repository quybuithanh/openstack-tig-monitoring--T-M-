# TIG Stack Monitoring Lab

Bài tập lớn mô phỏng giám sát hạ tầng Linux bằng Telegraf, InfluxDB và Grafana trên VirtualBox.

## Mô hình

```text
Máy Windows (host)
  |-- VirtualBox NAT (Internet để tải và cập nhật)
  `-- VirtualBox Host-only: 192.168.56.0/24
        |-- monitor-01: InfluxDB :8086, Grafana :3000, Telegraf
        |-- target-01: Telegraf
        |-- target-02: Telegraf
        `-- target-03: Telegraf
```

InfluxDB và Grafana đặt trên `monitor-01`; Telegraf rên cả bốn máy gửi metric về InfluxDB.

## Danh sách VM

| Hostname     | Vai trò                     | IP Host-only                                 | Trạng thái        | Phụ trách      |
| ------------ | --------------------------- | -------------------------------------------- | ----------------- | -------------- |
| `monitor-01` | InfluxDB, Grafana, Telegraf | `192.168.56.101` (đã thấy trên máy hiện tại) | Đang cấu hình TIG | Chưa phân công |
| `target-01`  | May muc tieu, Telegraf      | 192.168.56.103                               | Đã kiểm tra ping  | Chưa phân công |
| `target-02`  | May muc tieu, Telegraf      | 192.168.56.104                               | Đã kiểm tra ping  | Chưa phân công |
| `target-03`  | May muc tieu, Telegraf      | 192.168.56.105                               | Đã kiểm tra ping  | Chưa phân công |
## Phiên bản

- Ubuntu Server: 24.04 LTS (cần xác nhận trên từng VM).
- InfluxDB OSS: 2.x.
- Telegraf: 1.x.
- Grafana OSS: stable từ kho chính thức.

## Tiến độ

- [x] Tạo 4 VM và đặt hostname.
- [x] Kiểm tra ping nội bộ giữa monitor và target.
- [x] Cài và khởi tạo InfluxDB trên monitor-01.
- [x]Cài Telegraf trên monitor-01.
- [ ] Sửa lỗi `401 Unauthorized` đẻ Telegraf ghi metric vào bucket.
- [ ] Cài Grafana và kết nối với InfluxDB.
- [ ] Cài Telegraf trên ba target, xác nhận metric theo hostname.
- [ ] Cài Telegraf trên ba target, xác nhận metric theo hostname.

Telegraf đang chạy nhưng InfluxDB trả `401 Unauthorized`; pipeline chưa hoạt động hoàn chỉnh.

## Bắt đầu

1. Đọc [hướng dẫn mạng](docs/network.md).
2. Đọc [hướng dẫn TIG](docs/setup-tig.md).
3. Dùng [cấu hình Telegraf mau](telegraf/telegraf.conf.example). Không commit cấu hình thật có token.

## Bảo mật

- Không commit API token, mật khẩu, SSH private key, ISO hay đĩa ảo VM.
- Telegraf chỉ cần token có quyền Write trên bucket cần ghi.
- Nếu token đã lộ trong ảnh/chat/repository, thu hồi và tạo token mới.
- Không đưa secret vào GitHub; chỉ chia sẻ cấu hình mẫu.

## Tài liệu chính thức

- [InfluxDB OSS 2](https://docs.influxdata.com/influxdb/v2/install/)
- [Telegraf InfluxDB v2 output](https://docs.influxdata.com/telegraf/v1/output-plugins/influxdb_v2/)
- [Grafana tren Ubuntu](https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/)
- [Grafana InfluxDB data source](https://grafana.com/docs/grafana/latest/datasources/influxdb/)
