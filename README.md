# TIG Stack Monitoring Lab

Bai tap lon mo phong giam sat ha tang Linux bang Telegraf, InfluxDB va Grafana tren VirtualBox.

## Mo hinh

```text
May Windows (host)
  |-- VirtualBox NAT (Internet de cai cap nhat)
  `-- VirtualBox Host-only: 192.168.56.0/24
        |-- monitor-01: InfluxDB :8086, Grafana :3000, Telegraf
        |-- target-01: Telegraf
        |-- target-02: Telegraf
        `-- target-03: Telegraf
```

InfluxDB va Grafana dat tren `monitor-01`; Telegraf tren ca bon may gui metric ve InfluxDB.

## Danh sach VM

| Hostname     | Vai tro                     | IP Host-only                                 | Trang thai        | Phu trach      |
| ------------ | --------------------------- | -------------------------------------------- | ----------------- | -------------- |
| `monitor-01` | InfluxDB, Grafana, Telegraf | `192.168.56.101` (da thay tren may hien tai) | Dang cau hinh TIG | Chua phan cong |
| `target-01`  | May muc tieu, Telegraf      | 192.168.56.103                               | Da kiem tra ping  | Chua phan cong |
| `target-02`  | May muc tieu, Telegraf      | 192.168.56.104                               | Da kiem tra ping  | Chua phan cong |
| `target-03`  | May muc tieu, Telegraf      | 192.168.56.105                               | Da kiem tra ping  | Chua phan cong |

IP co the doi neu DHCP cap lai. Kiem tra bang `ip -4 addr` tren tung VM.

## Phien ban

- Ubuntu Server: 24.04 LTS (can xac nhan tren tung VM).
- InfluxDB OSS: 2.x.
- Telegraf: 1.x.
- Grafana OSS: stable tu kho chinh thuc.

## Tien do

- [x] Tao 4 VM va dat hostname.
- [x] Kiem tra ping noi bo giua monitor va target.
- [x] Cai va khoi tao InfluxDB tren monitor-01.
- [x] Cai Telegraf tren monitor-01.
- [ ] Sua loi `401 Unauthorized` de Telegraf ghi metric vao bucket.
- [ ] Cai Grafana va ket noi voi InfluxDB.
- [ ] Cai Telegraf tren ba target, xac nhan metric theo hostname.
- [ ] Tao dashboard, cap nhat IP va bo sung anh minh chung da che secret.

Telegraf dang chay nhung InfluxDB tra `401 Unauthorized`; pipeline chua hoat dong hoan chinh.

## Bat dau

1. Doc [huong dan mang](docs/network.md).
2. Doc [huong dan TIG](docs/setup-tig.md).
3. Dung [cau hinh Telegraf mau](telegraf/telegraf.conf.example). Khong commit cau hinh that co token.

## Bao mat

- Khong commit API token, mat khau, SSH private key, ISO hay dia ao VM.
- Telegraf chi can token co quyen Write tren bucket can ghi.
- Neu token da lo trong anh/chat/repository, thu hoi va tao token moi.
- Khong dua secret vao GitHub; chi chia se cau hinh mau.

## Tai lieu chinh thuc

- [InfluxDB OSS 2](https://docs.influxdata.com/influxdb/v2/install/)
- [Telegraf InfluxDB v2 output](https://docs.influxdata.com/telegraf/v1/output-plugins/influxdb_v2/)
- [Grafana tren Ubuntu](https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/)
- [Grafana InfluxDB data source](https://grafana.com/docs/grafana/latest/datasources/influxdb/)
