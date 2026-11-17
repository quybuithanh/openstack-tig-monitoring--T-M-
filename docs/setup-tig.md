# Cài đặt và cấu hình TIG

Hướng dẫn này ghi lại quá trình triển khai phòng lab Ubuntu Server trên VirtualBox. Các lệnh cài đặt gói phần mềm cần có kết nối Internet thông qua NAT và sử dụng kho phần mềm chính thức.

## Phân bổ

| Máy | Thành phần |
|---|---|
| `monitor-01` | InfluxDB OSS 2, Grafana OSS, Telegraf |
| `target-01` đến `target-03` | Telegraf |

## Cài đặt InfluxDB trên monitor-01

Đã cài đặt gói `influxdb2`, khởi động dịch vụ `influxdb` và khởi tạo hệ thống thông qua địa chỉ `http://<IP-monitor>:8086`.

Các giá trị dự kiến, cần đối chiếu với giao diện thực tế:

- Organization: `lab`
- Bucket: `system_metrics`
- Telegraf token: Token tùy chỉnh chỉ có quyền ghi (Write) trên bucket.

Không ghi token thật vào tài liệu, ảnh chụp màn hình, tệp cấu hình mẫu hoặc Git.

Token Operator đã bị lộ trong ảnh chụp màn hình thiết lập cần được thu hồi. Hãy sử dụng token mới với phạm vi quyền hạn được giới hạn phù hợp.

## Cấu hình Telegraf

Tệp cấu hình mẫu: [`telegraf/telegraf.conf.example`](../telegraf/telegraf.conf.example).

Tạo tệp cấu hình riêng tại `/etc/telegraf/telegraf.conf` và thiết lập hostname chính xác tương ứng với từng máy ảo.

Trên các máy target, URL phải trỏ đến địa chỉ IP Host-only của máy `monitor-01`. Trên máy `monitor-01`, có thể sử dụng `localhost`.

Thực hiện các lệnh sau:

```bash
sudo telegraf --config /etc/telegraf/telegraf.conf --test
sudo systemctl enable --now telegraf
sudo systemctl restart telegraf
sudo systemctl status telegraf
sudo journalctl -u telegraf -n 30 --no-pager
```

Lưu ý:

- Lệnh `telegraf --test` không gửi dữ liệu chỉ số (metric) đến đầu ra (output).
- Lỗi `401 Unauthorized` có nghĩa là InfluxDB từ chối xác thực hoặc quyền truy cập.
- Khi gặp lỗi này, hãy kiểm tra token, organization, bucket và quyền Write.
- Không chia sẻ token hoặc các tệp nhật ký (log) có chứa thông tin bí mật.

## Cài đặt và cấu hình Grafana

Cài đặt Grafana OSS từ [kho APT chính thức](https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/).

Sau khi cài đặt, khởi động dịch vụ bằng lệnh:

```bash
sudo systemctl enable --now grafana-server
```

Truy cập địa chỉ `http://<IP-monitor>:3000`, thay đổi mật khẩu trong lần đăng nhập đầu tiên và thêm nguồn dữ liệu InfluxDB (InfluxDB Data Source) với các thông tin sau:

- URL: `http://localhost:8086`
- Query language: Flux
- Organization/Bucket: Sử dụng tên thực tế đã cấu hình.
- Token: Sử dụng token Read riêng cho Grafana nếu đã cấu hình.

Nhấn **Save & Test** để lưu cấu hình và kiểm tra kết nối.

## Kiểm tra dữ liệu

Trong Grafana Explore hoặc InfluxDB Data Explorer, chọn bucket và thiết lập khoảng thời gian 15 phút.

Sử dụng truy vấn Flux sau để kiểm tra dữ liệu CPU:

```flux
from(bucket: "system_metrics")
  |> range(start: -15m)
  |> filter(fn: (r) => r._measurement == "cpu")
```

## Các công việc còn lại

- [ ] Khắc phục lỗi `401`, xác nhận dữ liệu metric được gửi thành công vào bucket.
- [ ] Cài đặt Telegraf trên ba máy target và kiểm tra metric theo từng hostname.
- [ ] Cài đặt Grafana và nhấn Save & Test để kiểm tra nguồn dữ liệu.
- [ ] Tạo dashboard theo dõi CPU, RAM, ổ đĩa (Disk) và mạng (Network).
- [ ] Ghi lại phiên bản phần mềm, địa chỉ IP thực tế và bổ sung ảnh minh chứng đã che thông tin bí mật.
