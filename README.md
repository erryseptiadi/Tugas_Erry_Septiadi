================================================================================
  SYSTEM MONITORING CUACA BMKG & REKOMENDASI PERAWATAN KEBUN HIDROPONIK
================================================================================
Otomasi Data Analyst Menggunakan n8n, Google Sheets, Gemini AI, dan Telegram

1. RINGKASAN PROYEK
--------------------------------------------------------------------------------
Perubahan cuaca harian seperti fluktuasi suhu ekstrem, kelembapan tinggi, maupun 
hujan mendadak sangat mempengaruhi kesehatan tanaman pada sistem urban farming 
dan hidroponik. Kurangnya pemantauan mikro-iklim secara real-time dapat 
mengakibatkan pembusukan akar akibat air nutrisi yang terlalu panas, serangan 
jamur daun, hingga gagal panen.

Proyek ini membangun sistem otomasi pemantauan cuaca berbasis AI (AI Weather & 
Farm Analyst) yang mengambil data prakiraan cuaca resmi dari BMKG, mencatat 
riwayat harian ke Google Sheets, dan menganalisis tren iklim menggunakan Google 
Gemini AI. Hasil analisis kemudian dikirimkan secara otomatis dalam bentuk 
instruksi perawatan kebun yang ramah dan praktis ke grup/chat Telegram pengelola 
kebun setiap pagi pukul 06.00 WIB.


2. ARSITEKTUR & ALUR KERJA (WORKFLOW PIPELINE)
--------------------------------------------------------------------------------
Sistem ini dibangun menggunakan platform otomasi n8n dengan alur kerja 6 tahap utama:

[1. Schedule Trigger] -> [2. HTTP Request BMKG] -> [3. Edit Fields / Parse]
                                                          |
                                                          v
[6. Telegram Alert] <-- [5. Gemini AI Agent] <-- [4. Google Sheets (Append & Get)]


Detail Peranan Node:

1. Get Rows (Data External / BMKG API)
   - Node: HTTP Request
   - Fungsi: Mengambil data prakiraan cuaca publik dari API resmi BMKG secara 
     berkala berdasarkan kode wilayah lokasi kebun (Kelurahan Kota Bambu Utara).

2. Data Parsing & Standardisasi
   - Node: Edit Fields / Code
   - Fungsi: Menyaring data mentah JSON dari BMKG menjadi format terstruktur 
     lokal (Tanggal/Waktu, Suhu °C, Kelembapan %, dan Deskripsi Cuaca).

3. Append Rows (Pencatatan Database)
   - Node: Google Sheets (Append Row)
   - Fungsi: Menyimpan setiap data cuaca harian yang baru diambil ke baris baru 
     spreadsheet Google Sheets sebagai basis data history.

4. Get History Rows (Pembacaan Tren Data)
   - Node: Google Sheets (Get Many)
   - Fungsi: Membaca kembali 7 baris data terakhir dari Google Sheets agar AI 
     dapat membandingkan tren cuaca hari ini dengan hari-hari sebelumnya.

5. Extract Insight (Analisis AI Agronomis)
   - Node: AI Agent (Google Gemini Chat Model)
   - Fungsi: Menganalisis riwayat dan data cuaca terbaru, lalu menghasilkan 
     rekomendasi tindakan spesifik bagi pengelola kebun (penanganan suhu tandon 
     nutrisi, pengaturan paranet, serta pencegahan jamur).

6. Notifikasi & Alerting
   - Node: Send a Message (Telegram)
   - Fungsi: Mengirimkan pesan ringkasan cuaca dan rekomendasi AI dengan gaya 
     bahasa yang santai, bersemangat, dan mudah dipahami ke aplikasi Telegram.


3. STRUKTUR DATA SPREADSHEET (GOOGLE SHEETS)
--------------------------------------------------------------------------------
Data cuaca dicatat secara otomatis pada Google Sheets dengan struktur kolom berikut:

tanggal             | kelurahan        | suhu | kelembapan | kondisi_cuaca
--------------------------------------------------------------------------------
2026-09-22 12:00:00 | Kota Bambu Utara | 34   | 58%        | Cerah
2026-09-23 12:00:00 | Kota Bambu Utara | 29   | 78%        | Berawan
2026-09-24 12:00:00 | Kota Bambu Utara | 26   | 90%        | Hujan Petir
2026-09-25 12:00:00 | Kota Bambu Utara | 28   | 80%        | Hujan Ringan


4. FORMAT PESAN NOTIFIKASI TELEGRAM
--------------------------------------------------------------------------------
Berikut adalah contoh pesan otomatis yang diterima oleh pengelola kebun:

--------------------------------------------------------------------------------
🌤️ HALO, SELAMAT PAGI TEMAN KEBUN! 🌿
Yuk cek kondisi cuaca & panduan perawatan tanamanmu hari ini! ✨

📍 Lokasi Kebun: Kota Bambu Utara
📅 Tanggal: 2026-09-29

─── 📊 STATUS CUACA HARI INI ───
• Kondisi: Cerah Berawan
• Suhu: 31°C
• Kelembapan: 68%

─── 💡 TIPS & REKOMENDASI AI HARI INI ───
1. Pagi & Siang Hari:
   Suhu udara diprediksi mencapai 31°C. Pastikan sirkulasi air nutrisi berjalan 
   lancar dan cek suhu air tandon agar tetap di bawah 28°C.
   
2. Sore Hari:
   Kelembapan udara berada pada 68%. Lakukan pemeriksaan rutin pada area daun 
   bagian bawah untuk mencegah potensi timbulnya jamur.

Tetap semangat merawat tanamannya hari ini ya! 🌱 Bismillah panen melimpah! 🚀
--------------------------------------------------------------------------------

5. NILAI TAMBAH & KESIMPULAN TUGAS
--------------------------------------------------------------------------------
1. Efisiensi Operasional: Mengubah data mentah BMKG menjadi instruksi tindakan 
   riil (actionable insights) tanpa perlu analisis manual.
2. Pencegahan Risiko: Memberikan peringatan dini cuaca ekstrem sehingga pengelola 
   kebun dapat melakukan tindakan pencegahan lebih awal.
3. Skalabilitas Workflow: Arsitektur pipeline ini mudah disesuaikan untuk sektor 
   bisnis lain yang sensitif terhadap cuaca, seperti logistik pengiriman, event 
   organizer outdoor, maupun lapangan olahraga.
================================================================================

## Import ke n8n

Import `Tugas_Erry_Septiadi.example.json`, lalu ganti placeholder dengan ID spreadsheet, nama atau ID sheet, Telegram chat ID, serta kredensial n8n. File `Tugas_Erry_Septiadi.json` adalah salinan lokal dan tidak dilacak Git.
