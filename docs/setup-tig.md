# Cai dat va cau hinh TIG

Huong dan nay ghi lai lab Ubuntu Server tren VirtualBox. Cac lenh cai goi can Internet qua NAT va kho phan mem chinh thuc.

## Phan bo

| May | Thanh phan |
|---|---|
| `monitor-01` | InfluxDB OSS 2, Grafana OSS, Telegraf |
| `target-01` den `target-03` | Telegraf |

## InfluxDB tren monitor-01

Da cai goi `influxdb2`, bat dich vu `influxdb` va khoi tao qua `http://<IP-monitor>:8086`.

Gia tri du kien, can doi chieu giao dien thuc te:

- Organization: `lab`
- Bucket: `system_metrics`
- Telegraf token: Custom token chi co quyen Write tren bucket.

Khong ghi token that vao tai lieu, anh chup, cau hinh mau hoac Git. Token Operator da lo trong anh setup can duoc thu hoi; dung token gioi han quyen moi.

## Telegraf

Cau hinh mau: [`telegraf/telegraf.conf.example`](../telegraf/telegraf.conf.example). Tao cau hinh rieng tai `/etc/telegraf/telegraf.conf`; dat hostname dung voi VM. Tren target, URL phai tro den IP Host-only cua monitor; tren monitor co the dung localhost.

```bash
sudo telegraf --config /etc/telegraf/telegraf.conf --test
sudo systemctl enable --now telegraf
sudo systemctl restart telegraf
sudo systemctl status telegraf
sudo journalctl -u telegraf -n 30 --no-pager
```

`telegraf --test` khong gui metric den output. Loi `401 Unauthorized` co nghia InfluxDB tu choi xac thuc/quyen; kiem tra token, org, bucket va quyen Write. Khong chia se token hoac log co secret.

## Grafana

Cai Grafana OSS tu [kho APT chinh thuc](https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/), sau do:

```bash
sudo systemctl enable --now grafana-server
```

Mo `http://<IP-monitor>:3000`, doi mat khau lan dau va them InfluxDB data source:

- URL: `http://localhost:8086`
- Query language: Flux
- Organization/bucket: dung ten thuc te
- Token: token Read rieng cho Grafana neu cau hinh

Bam Save & test.

## Kiem tra du lieu

Trong Grafana Explore hoac InfluxDB Data Explorer, chon bucket va khoang thoi gian 15 phut:

```flux
from(bucket: "system_metrics")
  |> range(start: -15m)
  |> filter(fn: (r) => r._measurement == "cpu")
```

## Viec con lai

- [ ] Sua 401, xac nhan metric vao bucket.
- [ ] Cai Telegraf tren ba target, kiem tra metric theo hostname.
- [ ] Cai Grafana, Save & test data source.
- [ ] Tao dashboard CPU, RAM, disk, network.
- [ ] Ghi phien ban, IP thuc te va anh minh chung da an secret.
