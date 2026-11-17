# Mạng VirtualBox

## Mô hình

Mỗi máy ảo (VM) sử dụng hai bộ điều hợp mạng (Adapter):

1. Adapter 1: NAT để Ubuntu truy cập Internet khi cập nhật hệ thống hoặc cài đặt các gói cần thiết.
2. Adapter 2: Host-only để máy tính chủ (Host) và bốn máy ảo trao đổi dữ liệu trong mạng phòng lab.

Chọn cùng một mạng Host-only cho cả bốn máy ảo. Mạng đang sử dụng có dải địa chỉ `192.168.56.0/24`; kiểm tra địa chỉ IP thực tế trên từng máy, không tự gán địa chỉ theo ví dụ.

## Kiểm tra trên từng máy ảo

```bash
hostname
ip -4 addr
ip route
```

Ghi địa chỉ IP của giao diện mạng Host-only vào bảng README.

Lệnh `ip route` cần hiển thị tuyến đường mặc định (default route) thông qua NAT để máy ảo có thể truy cập Internet.

## Kiểm tra kết nối nội bộ

Từ máy ảo `monitor-01`, ping từng máy đích bằng địa chỉ IP Host-only:

```bash
ping -c 4 192.168.56.103
ping -c 4 192.168.56.104
ping -c 4 192.168.56.105
```

Ping thành công xác nhận kết nối IP cơ bản giữa các máy ảo.

Nếu ping thất bại, hãy kiểm tra:

- Máy ảo đích đang hoạt động.
- Adapter 2 đã được bật.
- Cả bốn máy ảo đang sử dụng cùng một mạng Host-only.

## Cổng dịch vụ

- InfluxDB: TCP `8086` cho phép các máy ảo kết nối đến `monitor-01`.
- Grafana: TCP `3000` cho phép máy tính chủ hoặc mạng được cấp quyền truy cập.

Chỉ cho phép truy cập trong phạm vi mạng phòng lab; không mở trực tiếp các dịch vụ ra Internet.

- InfluxDB UI: `http://192.168.56.101:8086`
- Grafana UI: `http://192.168.56.101:3000`

## Lưu ý

Mạng Host-only chỉ kết nối máy ảo với máy tính chủ VirtualBox trên cùng một máy tính vật lý; không kết nối trực tiếp ba máy tính của các thành viên khác.

Mỗi thành viên có thể tự triển khai và chạy một phiên bản phòng lab riêng trên máy tính của mình.

Để thực hiện demo chung, các thành viên cần sử dụng mạng hoặc VPN được cho phép để kết nối các máy tính với nhau.
