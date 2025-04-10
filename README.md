# 📘 K3s Etcd Snapshot - Step by Step

## 🗓 Tanggal
10 April 2025

## 🖥️ Lingkungan
- Node tunggal dengan hostname: `server1`
- IP: `10.10.51.153`
- K3s dengan embedded etcd
- Ubuntu 22.04 LTS

## 📝 Tujuan
Melakukan pengujian perintah snapshot pada embedded etcd di K3s, serta memahami bagaimana proses backup dan restore berjalan.

---

## 🔧 Langkah-Langkah

### 1. Cek Status K3s
```bash
sudo systemctl status k3s
```

### 2. Cek Proses Snapshot Otomatis
K3s dikonfigurasi dengan parameter berikut:
```yaml
--etcd-snapshot-schedule-cron "0 */6 * * *"
--etcd-snapshot-retention 5
--etcd-snapshot-dir /var/lib/rancher/k3s/etcd-snapshots
```

### 3. Lakukan Snapshot Manual
```bash
sudo k3s etcd-snapshot save --name manual-test-server1
```

### 4. Verifikasi File Snapshot
```bash
ls -lh /var/lib/rancher/k3s/etcd-snapshots
```
Contoh output:
```bash
-rw------- 1 root root 51M Apr 10 15:08 manual-test-server1-1744272483
```

### 5. Cek Daftar Snapshot
```bash
sudo k3s etcd-snapshot ls --etcd-snapshot-dir /var/lib/rancher/k3s/etcd-snapshots
```
Contoh output:
```bash
Name                           Location                                                                  Size     Created
manual-test-server1-1744272483 file:///var/lib/rancher/k3s/etcd-snapshots/manual-test-server1-1744272483 53297184 2025-04-10T15:08:03+07:00
```

### 6. Melihat Log Snapshot
```bash
journalctl -u k3s -f
```

---

## ✅ Kesimpulan
- Snapshot berhasil dilakukan manual menggunakan perintah `k3s etcd-snapshot save`.
- File snapshot tersimpan di direktori `/var/lib/rancher/k3s/etcd-snapshots`.
- Snapshot dapat diverifikasi melalui CLI dan dicek juga ukuran serta timestamp-nya.

---

## 📌 Catatan Tambahan
- Snapshot otomatis membutuhkan cron dan waktu berjalan, jadi untuk pengujian manual lebih disarankan.
- File snapshot dapat dipakai untuk proses restore jika diperlukan dengan perintah `k3s server --cluster-reset --cluster-reset-restore-path=...`.
