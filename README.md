# Sistem Deteksi Objek & Kendali Terintegrasi - Gamantaray

Proyek integrasi sistem Computer Vision berbasis **OpenCV**, kontrol aktuator LED GPIO pada **Raspberry Pi 4B**, dashboard **Web Monitoring (Flask)** realtime, serta komunikasi kontrol **Pixhawk MAVLink**.

Sistem deteksi objek ini sepenuhnya menggunakan **OpenCV (HSV Color Segmentation & Contour Analysis)** yang ringan, efisien, dan memiliki latensi sangat rendah, menggantikan model deep learning berbasis YOLO agar dapat berjalan mulus langsung di Raspberry Pi tanpa keterbatasan daya komputasi.

---

## 🚀 Fitur Utama

1. **Deteksi Objek Berbasis OpenCV (HSV)**:
   - **Bola Merah**: Mengontrol LED Merah (GPIO 17).
   - **Bola Hijau**: Mengontrol LED Hijau (GPIO 27).
   - **Kotak Biru**: Mengontrol LED Biru (GPIO 22) dan otomatis mengambil foto (*trigger*) lalu mengunggahnya ke server web monitoring.
2. **Logika Anti-Kedip (*State Persistence / Hold Filter*)**:
   - Menghindari LED berkedip (*flickering*) saat terjadi *frame drop* atau oklusi sesaat (hold time: 0.8 detik).
3. **Hardware Actuator (Raspberry Pi GPIO)**:
   - Terintegrasi modul `RPi.GPIO` dengan *graceful fallback* ke mode simulasi jika dijalankan di PC/Laptop non-Pi.
4. **Web Monitoring Dashboard**:
   - Menampilkan status deteksi objek, indikator status LED, dan galeri foto hasil deteksi Kotak Biru secara realtime.
5. **Kontrol Pixhawk & Teleoperasi**:
   - Komunikasi MAVLink dengan Pixhawk (`pixhawk_bridge.py`).
   - Kontrol joystick Ground Control Station via Pygame (`gcs_joystick.py`).

---

## 📁 Struktur Direktori

```text
├── control/
│   ├── gcs_joystick.py          # Kontrol joystick teleoperasi (Pygame)
│   └── pixhawk_bridge.py        # Komunikasi MAVLink ke Pixhawk
├── hardware/
│   └── led_controller.py        # Kontrol pin GPIO Raspberry Pi 4B (LED RGB)
├── vision/
│   ├── calibrate_hsv.py         # Alat kalibrasi interaktif rentang warna HSV
│   └── detect_objects.py        # Detektor utama berbasis OpenCV HSV & kontur
├── web_monitoring/
│   ├── app.py                   # Server Flask dashboard monitoring
│   ├── templates/
│   │   └── index.html           # Tampilan antarmuka dashboard monitoring
│   └── uploads/                 # Direktori penyimpanan foto yang diunggah
├── .gitignore
├── README.md
└── requirements.txt             # Dependensi Python
```

---

## 🛠️ Instalasi & Persiapan

### 1. Clone Repositori
```bash
git clone <URL_REPO_GITHUB_BARU>
cd <NAMA_FOLDER_REPO>
```

### 2. Buat Virtual Environment & Install Dependensi
```bash
python -m venv venv

# Mengaktifkan venv (Linux / Raspberry Pi OS):
source venv/bin/activate

# Mengaktifkan venv (Windows):
venv\Scripts\activate

# Install dependensi:
pip install -r requirements.txt
```

*Catatan khusus Raspberry Pi:*
Jika menggunakan Raspberry Pi OS, pastikan paket GPIO terpasang:
```bash
pip install RPi.GPIO
```

---

## ⚡ Panduan Menjalankan Sistem

### 1. Kalibrasi Rentang Warna HSV (Opsional)
Jika kondisi pencahayaan di ruangan/kolam berubah, gunakan script kalibrasi untuk mencari nilai HSV yang pas:
```bash
python vision/calibrate_hsv.py
```
Geser trackbar hingga objek tersegmentasi dengan baik, lalu perbarui konstanta rentang warna di `vision/detect_objects.py`.

### 2. Menjalankan Web Server Monitoring
Jalankan server Flask di laptop atau Raspberry Pi:
```bash
python web_monitoring/app.py
```
Buka browser dan akses: `http://localhost:5000` (atau `http://<IP_RASPBERRY_PI>:5000`).

### 3. Menjalankan Deteksi Objek OpenCV
Jalankan modul deteksi pada perangkat yang terhubung dengan kamera:
```bash
# Menjalankan dengan tampilan jendela GUI (Laptop / Desktop):
python vision/detect_objects.py --server http://localhost:5000

# Menjalankan mode headless (melalui SSH di Raspberry Pi tanpa layar):
python vision/detect_objects.py --server http://localhost:5000 --no-gui
```

### 4. Menguji Aktuator LED (Self-Test)
Untuk menguji jalur kabel fisik LED pada Raspberry Pi:
```bash
python hardware/led_controller.py
```

### 5. Menjalankan Kontrol Pixhawk & Joystick
```bash
# Terminal 1: Bridge Pixhawk
python control/pixhawk_bridge.py

# Terminal 2: Joystick GCS
python control/gcs_joystick.py
```

---

## 📌 Skema Pin GPIO LED (Raspberry Pi 4B)

| Objek Deteksi | Warna LED | Pin BCM | Pin Fisik Board |
| :--- | :--- | :--- | :--- |
| Bola Merah | Merah | GPIO 17 | Pin 11 |
| Bola Hijau | Hijau | GPIO 27 | Pin 13 |
| Kotak Biru | Biru | GPIO 22 | Pin 15 |
| - | Ground (GND) | GND | Pin 6 / 9 / 14 / 20 |

---

## 📄 Lisensi
Proyek ini dikembangkan untuk keperluan Magang Gamantaray.
