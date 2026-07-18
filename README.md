# Auto Trade Flexi — APEX Trading Intelligence

Bot scanner & simulator trading otomatis untuk pasar **Indodax** (Rupiah/IDR), berbasis Python (Flask) dengan notifikasi Telegram dan dashboard web real-time. Bot ini memantau seluruh pasangan `*/IDR` di Indodax, menghasilkan sinyal akumulasi/distribusi berbasis RSI, volume, dan Market Power Index (MPI), lalu mengeksekusi **virtual/paper trade** otomatis saat sinyal memenuhi kriteria kualitas tertinggi.

## Fitur Utama

- **Scanner otomatis** seluruh pair `*/IDR` di Indodax menggunakan data OHLCV 1 jam (via `ccxt`).
- **Sinyal trading** berbasis kombinasi:
  - RSI (14) — deteksi kondisi oversold (`STRONG ACCUMULATION`) / overbought (`DISTRIBUTION / SELL`).
  - MPI (Market Power Index) — rasio volume candle hijau vs merah.
  - Volume spike ratio — mendeteksi lonjakan volume dibanding rata-rata 20 candle.
  - Sistem grading sinyal: `A+ (PERFECT)`, `B (EARLY)`, `C (LOW)`.
- **Take Profit adaptif bertingkat (TP1/TP2/TP3)** dan **Stop Loss trailing otomatis** (SL bergeser ke entry setelah TP1, ke TP1 setelah TP2).
- **Virtual account / paper trading engine** — simulasi saldo, margin, PnL, win-rate, dan histori trade tersimpan lokal di `virtual_account_indodax.json` (mulai dari saldo virtual $1000, tanpa uang riil).
- **Auto-execute trade** untuk sinyal grade `A+ (PERFECT)` saja, dengan batas maksimum posisi terbuka dan alokasi margin per trade.
- **Notifikasi Telegram** (multi chat ID) untuk sinyal baru, TP hit, dan penutupan posisi, lengkap dengan tombol tautan ke chart Indodax.
- **Dashboard web** ("APEX Trading Intelligence") dengan proteksi Basic Auth, menampilkan:
  - Ringkasan akun (balance, equity, margin terpakai, unrealized PnL, return %).
  - Tabel posisi terbuka & histori trade.
  - Tabel sinyal pasar real-time (RSI, MPI, volume spike, grade).
  - Endpoint reset akun virtual kembali ke saldo awal.
- Konversi tampilan harga otomatis IDR ↔ USD menggunakan kurs referensi.

> Catatan: Semua eksekusi order pada bot ini bersifat **simulasi (paper trading)** — bot tidak mengirim order beli/jual sungguhan ke Indodax. Cocok untuk menguji strategi sinyal sebelum dipakai dengan dana nyata.

## Tech Stack

- **Python 3** + **Flask** — web server & REST API dashboard.
- **ccxt** — koneksi ke exchange Indodax (fetch market & OHLCV).
- **pandas** — pemrosesan data candlestick dan perhitungan indikator (RSI, SMA, MPI).
- **pyTelegramBotAPI (telebot)** — bot & notifikasi Telegram.
- **python-dotenv** — memuat konfigurasi dari file environment.
- **HTML/CSS/JS (Chart.js)** — dashboard frontend (`templates/index.html`).

## Instalasi & Menjalankan

1. **Clone repository**
   ```bash
   git clone https://github.com/Dhiyaahaq33/auto-trade-flexi.git
   cd auto-trade-flexi
   ```

2. **Buat virtual environment (opsional tapi disarankan)**
   ```bash
   python -m venv venv
   source venv/bin/activate    # Linux/Mac
   venv\Scripts\activate       # Windows
   ```

3. **Install dependency**
   ```bash
   pip install ccxt pandas flask pyTelegramBotAPI python-dotenv urllib3
   ```

4. **Buat file konfigurasi `DATA.env`** di root project (lihat daftar variabel di bawah). File ini **tidak boleh di-commit** ke Git (sudah masuk `.gitignore`).

5. **Jalankan bot**
   ```bash
   python auto_trade.py
   ```
   Dashboard dapat diakses di `http://localhost:8000` (atau port sesuai variabel `PORT`), dengan login Basic Auth (`admin` + password dari `WEB_PASSWORD`).

## Konfigurasi Environment (`DATA.env`)

Buat file `DATA.env` di root project berisi variabel berikut (isi dengan nilai milikmu sendiri, **jangan pernah membagikan nilai asli**):

```env
TOKEN_HIGH=isi_token_bot_telegram_anda
CHAT_ID=isi_chat_id_telegram_anda_pisahkan_koma_jika_lebih_dari_satu
WEB_PASSWORD=isi_password_dashboard_anda
PORT=8000
```

| Variabel | Keterangan |
|---|---|
| `TOKEN_HIGH` | Token bot Telegram (dari BotFather) untuk mengirim notifikasi sinyal & trade. |
| `CHAT_ID` | Satu atau beberapa Chat ID Telegram tujuan notifikasi (pisahkan dengan koma). |
| `WEB_PASSWORD` | Password login dashboard web (username tetap `admin`). |
| `PORT` | (Opsional) Port server Flask, default `8000`. |

## Disclaimer

Bot ini dibuat untuk tujuan edukasi dan simulasi strategi trading (paper trading). Sinyal yang dihasilkan **bukan merupakan nasihat finansial**. Trading aset kripto memiliki risiko tinggi termasuk potensi kehilangan seluruh modal. Gunakan dengan tanggung jawab sendiri, dan lakukan riset mandiri (DYOR) sebelum mengambil keputusan finansial apa pun.
