
# Monitoring MikroTik menggunakan Prometheus, SNMP Exporter dan Grafana

## Deskripsi
Proyek ini merupakan implementasi monitoring perangkat MikroTik menggunakan stack monitoring:
- Prometheus (pengumpulan metrics)
- SNMP Exporter (mengambil data SNMP dari MikroTik)
- Grafana (visualisasi data)

## Persyaratan
- Docker dan Docker Compose terinstall
- MikroTik dengan SNMP enabled
- Port yang dibutuhkan:
  - 9090 (Prometheus)
  - 9116 (SNMP Exporter)
  - 3000 (Grafana)

## Cara Penggunaan

### 1. Konfigurasi MikroTik
Login ke MikroTik dan aktifkan SNMP:
```bash
/snmp set enabled=yes
/snmp set version=2
/snmp set community=public
```

### 2. Setup Monitoring Stack

1. Clone repository ini:
```bash
git clone https://github.com/khairuls17/Docker-compose-SPG.git
cd Docker-compose-SPG
```

2. Sesuaikan konfigurasi Prometheus (prometheus.yml):
````yaml
global:
  scrape_interval: 60s
  evaluation_interval: 60s

scrape_configs:
  - job_name: 'snmp-mikrotik'
    honor_labels: true
    metrics_path: /snmp
    params:
      module: [mikrotik]
    static_configs:
      - targets:
          - 192.168.10.1  # Ganti dengan IP MikroTik Anda
          - ip address mikrotik lainnya  
        labels:
          site: kantor-pusat #sesuaikan labelnya
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - target_label: __address__
        replacement: snmp-exporter:9116
      - source_labels: [__param_target]
        target_label: instance
````

3. Jalankan stack monitoring:
```bash
docker-compose up -d
```

### 3. Cek status di Prometheus

1. Buka Prometheus di browser: `http://localhost:9090`
2. Buka Target health cek statusnya dan target nya pastikan up


### 4. Setup Grafana

1. Buka Grafana di browser: `http://localhost:3000`
2. Login dengan kredensial default:
   - Username: `admin`
   - Password: `admin`

3. Tambahkan Prometheus sebagai Data Source:
   - Menu Configuration > Data Sources
   - Add data source > Prometheus
   - URL: `http://prometheus:9090`
   - Save & Test

4. Import Dashboard:
   - Menu + > Import
   - Upload JSON file atau gunakan ID dashboard dari Grafana.com
   - Pilih Prometheus data source
   - Import

## Struktur Folder
```
monitoring-jaringan/
├── docker-compose.yml
├── config/
│   ├── prometheus/
│       └── prometheus.yml
│   
│       
└── README.md
```

## Troubleshooting

1. Cek status container:
```bash
docker-compose ps
-> pastikan container statusnya up
```

2. Lihat logs:
```bash
docker-compose logs prometheus
docker-compose logs snmp-exporter
```


## Kontribusi
Silakan buat issue atau pull request untuk kontribusi.

---
*Catatan: Ganti semua IP address dan kredensial sesuai dengan environment Anda.*