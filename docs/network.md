# Mang VirtualBox

## Mo hinh

Moi VM dung hai adapter:

1. Adapter 1: NAT de Ubuntu truy cap Internet khi cap nhat/cai goi.
2. Adapter 2: Host-only de host va bon VM trao doi trong mang lab.

Chon cung mot Host-only network cho ca bon VM. Mang dang dung co dai `192.168.56.0/24`; doc IP thuc te tren tung may, khong tu gan theo vi du.

## Kiem tra tren tung VM

```bash
hostname
ip -4 addr
ip route
```

Ghi IP cua interface Host-only vao bang README. `ip route` nen co default route tren NAT de truy cap Internet.

## Kiem tra noi bo

Tu `monitor-01`, ping tung target bang IP Host-only:

```bash
ping -c 4 192.168.56.103
ping -c 4 192.168.56.104
ping -c 4 192.168.56.105
```

Ping thanh cong xac nhan ket noi IP co ban. Neu that bai, kiem tra VM dang chay, Adapter 2 da bat va ca bon VM dung cung Host-only network.

## Cong dich vu

- InfluxDB: TCP 8086 tu cac VM den monitor-01.
- Grafana: TCP 3000 tu host/mang duoc cho phep.

Chi cho phep tren mang lab; khong mo truc tiep ra Internet.

- InfluxDB UI: `http://192.168.56.101:8086`
- Grafana UI: `http://192.168.56.101:3000`

Host-only chi noi VM voi host VirtualBox tren cung may; no khong ket noi ba may tinh cua cac thanh vien. Moi nguoi co the chay ban lab rieng. Demo chung can mang/VPN duoc chap nhan.
