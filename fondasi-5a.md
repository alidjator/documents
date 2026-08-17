# Employee Self Service — Business Process Narrative

## 1. Ringkasan & Ruang Lingkup

Employee Self Service (ESS) adalah lapisan antarmuka terpadu bagi karyawan — padanan Client Portal tapi untuk sisi karyawan, bukan pemilik proses bisnis baru. ESS sudah dirujuk berulang kali di berbagai domain lain sebagai kanal pengajuan (cuti, resign, referral, cash advance, koperasi, booking aset, penggantian equipment, payroll query, komplain); domain ini tidak mengulang proses bisnis di baliknya — proses tersebut tetap kepemilikan masing-masing domain asal — melainkan mendefinisikan bagaimana karyawan mengakses & berinteraksi dengan seluruh titik itu sebagai satu pengalaman digital yang konsisten. Ruang lingkup mencakup: tujuan & filosofi, peta modul yang diagregasi, akses multi-kanal untuk kesenjangan digital, onboarding digital, unified dashboard, arsitektur komunikasi (notifikasi vs pengumuman), akses dokumen pribadi, autentikasi & keamanan akun, consent kepatuhan data pribadi dari sisi karyawan, cakupan akses per status, dan reporting pemakaian.

---

## 2. Tujuan & Filosofi ESS

Satu titik akses terpadu bagi karyawan untuk mengajukan permintaan, memantau status, dan menerima informasi — dengan prinsip yang sama seperti Client Portal (domain Client Portal, bagian Tujuan & Filosofi): mengurangi beban HR menjawab pertanyaan status satu-satu, krusial di volume tinggi khas outsourcing/BPO.

---

## 3. Peta Modul yang Diagregasi

ESS bukan proses baru, melainkan titik akses terpadu untuk transaksi yang sudah didefinisikan di domain lain:

| Modul | Kepemilikan Proses |
|---|---|
| Pengajuan cuti/izin/sakit & koreksi absensi | Time Management (Tahap Core Cycle) |
| Resign | Offboarding |
| Referral | Domain rekrutmen (B.3) |
| Cash Advance | Domain Payroll (F.6) |
| Koperasi | Domain Payroll (F.5) |
| Booking aset GA | General Affairs |
| Penggantian ID card/equipment | Employee Equipment & ID Card Distribution |
| Tiket helpdesk | Internal Helpdesk |
| Akses slip gaji & dokumen pribadi | Bagian 8 (di bawah) |

**Business rule**: daftar ini murni peta rujukan, bukan requirement baru — setiap perubahan cakupan modul ESS otomatis mengikuti perubahan di domain asalnya, tanpa perlu perubahan terpisah di lapisan ESS itu sendiri (mempertahankan prinsip ESS sebagai lapisan antarmuka, bukan pemilik logika bisnis).

**Integrasi antar tahap**: peta modul ini adalah titik rujukan lintas domain (Time Management, Offboarding, rekrutmen, Payroll, General Affairs, Employee Equipment & ID Card Distribution, Internal Helpdesk) — ESS hanya menampilkan & meneruskan, bukan memroses ulang logika masing-masing domain. **Jalur kegagalan**: jika terjadi perubahan cakupan/alur proses di salah satu domain asal namun tampilan/konfigurasi modul di ESS belum ter-update mengikutinya, dapat timbul *mismatch* antara apa yang ditampilkan ke karyawan di ESS dan proses aktual yang berjalan di domain asal (mis. field/status yang sudah tidak berlaku di domain asal tapi masih tampil di ESS, atau sebaliknya) — kondisi ini wajib dianggap *drift* konfigurasi yang direkonsiliasi segera begitu terdeteksi (mis. lewat pengecekan berkala oleh HR Ops/IT), bukan dibiarkan sampai keluhan karyawan muncul.

---

## 4. Sub-Siklus: Multi-Channel Access & Kesenjangan Digital Karyawan

**Pre-Cycle**: profil kesenjangan digital per site dipetakan — mayoritas tenaga lapangan (security/cleaning/buruh) belum tentu memiliki smartphone pribadi/paket data memadai.

**Life Cycle**:
- **Actor**: HR Ops (penyedia kanal alternatif), IT (infrastruktur teknis).
- **Proses bisnis**: opsi kanal alternatif disediakan berjenjang sesuai tingkat kesenjangan — WhatsApp Business API untuk notifikasi & pengajuan sederhana, terminal/kiosk bersama di kantor site untuk karyawan tanpa device pribadi, mode *low-bandwidth* untuk site dengan konektivitas terbatas.
- **Business rule & validasi**: desain ini melengkapi pola *hybrid channel* yang sudah diterapkan untuk Client Portal (domain Client Portal, bagian Desain Inklusif), namun disesuaikan realita sisi karyawan yang justru berpotensi **lebih rentan** dibanding PIC client — asumsi bahwa pengguna ESS punya device/koneksi semapan pengguna Client Portal (PIC client/site supervisor) tidak berlaku dan wajib dihindari dalam desain.

**Integrasi antar tahap**: kanal alternatif (WhatsApp Business API, kiosk/terminal site, infrastruktur IT internal) bergantung pada pihak ketiga (penyedia API WhatsApp) maupun infrastruktur IT site sendiri, di luar kendali penuh proses bisnis ESS. **Jalur kegagalan**: jika salah satu kanal alternatif tidak tersedia (mis. WhatsApp Business API down, kiosk site rusak/offline, atau infrastruktur IT site mengalami gangguan), karyawan pada site tersebut wajib memiliki jalur cadangan berjenjang berikutnya (mis. dari kiosk turun ke bantuan langsung HR Ops/Site Supervisor secara manual) — bukan dibiarkan tanpa akses sampai kanal utama pulih; insiden downtime kanal dicatat sebagai bagian evaluasi adopsi (bagian 12) untuk membedakan adopsi rendah akibat gangguan teknis vs akibat kesenjangan digital murni.

**Dokumen/output**: peta kanal alternatif per site, dikonsumsi sebagai basis Reporting & Analytics Pemakaian ESS (bagian 10).

**Post-Cycle**: efektivitas kanal alternatif dievaluasi berkala berdasarkan data adopsi (bagian 10) — site dengan adopsi rendah meski sudah disediakan kanal alternatif mengindikasikan kebutuhan intervensi tambahan (edukasi, bukan lagi soal ketersediaan kanal).

---

## 5. Sub-Siklus: Digital Onboarding ke ESS

**Pre-Cycle**: karyawan baru sudah melalui proses onboarding dasar dan datanya sudah tercatat sebagai aktif (domain Employee Data Governance).

**Life Cycle**:
- **Actor**: HR Ops/Site Supervisor (pendamping), IT (provisioning kredensial), karyawan baru.
- **Proses bisnis**: pembuatan kredensial akses dilakukan sebagai bagian dari proses provisioning akun saat onboarding (domain IT Asset & System Access Provisioning); karyawan menjalani *first-login* dan reset password wajib; tutorial dasar penggunaan diberikan, terutama penting untuk karyawan dengan literasi digital rendah, sebagai bagian dari Training Induction (domain Training).
- **Business rule & validasi**: bagi karyawan yang belum bisa mengakses secara mandiri di hari pertama, tersedia jalur alternatif — dibantu HR Ops/site supervisor sampai karyawan tersebut mandiri, bukan dibiarkan tanpa akses sampai belajar sendiri.

**Approval Workflow**: tidak memerlukan approval — bersifat provisioning standar mengikuti proses onboarding.

**Notifikasi/trigger**: notifikasi ke karyawan berisi kredensial awal & instruksi first-login.

**Dokumen/output**: akun ESS aktif, catatan penyelesaian tutorial dasar.

**Post-Cycle**: akun yang sudah aktif masuk siklus pemakaian rutin (bagian 6-8) dan siklus keamanan berkelanjutan (bagian 7).

---

## 6. Unified Dashboard/Inbox

**Pre-Cycle**: prasyarat tampilnya data konsolidasi adalah seluruh modul sumber (bagian 3) sudah mengirimkan status pengajuan terkini ke lapisan ESS, serta pemetaan peran approver (Site Supervisor/Team Leader/Dept Head, mengikuti Garis Komando) sudah tersedia dari domain Employee Data Governance.

**Life Cycle**:
- **Actor**: karyawan (pemilik pengajuan), Site Supervisor/Team Leader/Dept Head (approver, mengikuti Garis Komando), sistem (agregasi tampilan).
- **Proses bisnis**: satu tampilan konsolidasi untuk dua kebutuhan: **(a)** status seluruh pengajuan pribadi karyawan lintas modul (bagian 3), dan **(b)** khusus karyawan yang juga berperan sebagai approver — antrian approval pending yang perlu tindakan, sehingga tidak perlu memeriksa satu-satu ke tiap modul terpisah.
- **Business rule & validasi**: fitur ini adalah versi karyawan dari Unified Client Approval Queue yang didefinisikan pada domain Client Portal — kedua fitur memakai prinsip desain yang sama (konsolidasi lintas kategori dalam satu antrian untuk mengurangi risiko item tersembunyi), tapi populasi penggunanya berbeda (karyawan/approver internal vs PIC client eksternal), sehingga diimplementasikan sebagai dua permukaan terpisah yang konsisten prinsipnya, bukan satu permukaan yang dipaksakan sama untuk dua populasi berbeda.

**Approval Workflow**: dashboard ini sendiri bukan titik keputusan approval — tidak memerlukan approval untuk diakses/ditampilkan; approval atas tiap item pengajuan tetap tunduk pada alur approval domain asalnya masing-masing (bagian 3), dashboard hanya mengagregasi status & antrian tersebut.

**Notifikasi/trigger**: notifikasi ke approver saat ada item baru masuk antrian pending, serta pengingat berkala jika item pending mendekati/melewati SLA domain asal.

**Dokumen/output**: tampilan status konsolidasi per karyawan, antrian approval pending per approver.

**Post-Cycle**: status yang sudah final (disetujui/ditolak/selesai) di domain asal tercermin otomatis dan hilang dari antrian pending; riwayat status yang sudah selesai tetap dapat ditelusuri dari modul sumber masing-masing (bagian 3), bukan diarsipkan ulang di lapisan dashboard.

---

## 7. Sub-Siklus: Arsitektur Komunikasi Terkonsolidasi (Notifikasi & Pengumuman)

Dua jenis komunikasi dengan karakter berbeda yang sering keliru disatukan:

### 7.1 Notifikasi (Personal/Transaksional)

**Pre-Cycle**: taksonomi jenis notifikasi (mis. status pengajuan, pengingat SLA, panggilan Surat Peringatan, batas waktu *clearance* offboarding, login dari device baru) beserta tingkat urgensinya (kritikal vs non-kritikal) ditetapkan sebagai kebijakan baku terlebih dahulu, menjadi acuan kanal default per jenis (bagian 4).

**Life Cycle**:
- **Actor**: sistem (pemicu & pengiriman), karyawan (penerima, pengatur preferensi).
- **Proses bisnis**: notifikasi ditujukan ke individu spesifik berdasarkan transaksi yang relevan ke dirinya — kanal default per jenis mengikuti opsi kanal (bagian 4: push app vs WhatsApp vs SMS), karyawan dapat mengatur preferensi sendiri untuk notifikasi non-kritikal.
- **Business rule & validasi**: notifikasi kritikal (panggilan Surat Peringatan, batas waktu *clearance* offboarding) yang tidak direspons dalam SLA tertentu memicu eskalasi — preferensi karyawan untuk menonaktifkan notifikasi non-kritikal tidak berlaku untuk kategori kritikal ini.

**Approval Workflow**: pengiriman notifikasi individual tidak memerlukan approval — bersifat otomatis mengikuti trigger transaksi di domain asal; hanya penetapan/perubahan taksonomi jenis & kebijakan urgensi (Pre-Cycle) yang memerlukan otorisasi HR Corporate sebelum berlaku.

**Integrasi antar tahap**: notifikasi transaksional bersumber dari trigger di domain asal masing-masing (Time Management, Offboarding, IT Asset & System Access Provisioning, dst — bagian 3) dan disalurkan lewat kanal bersama (bagian 4, 7.3) ke Unified Dashboard/Inbox (bagian 6). **Jalur kegagalan**: jika notifikasi gagal terkirim sama sekali (bukan sekadar tidak direspons) — mis. kegagalan kanal WhatsApp Business API/push app, atau device karyawan tidak terjangkau — sistem wajib mencatat kegagalan pengiriman tersebut dan melakukan percobaan ulang melalui kanal alternatif (bagian 4); untuk notifikasi kategori kritikal, kegagalan pengiriman berulang setelah percobaan ulang dieskalasi ke HR Ops/Site Supervisor agar penyampaian dilakukan manual, bukan dianggap selesai hanya karena sistem sudah memicu pengiriman.

**Data/field — Preferensi Notifikasi**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Karyawan | String (ref.) | EMP-00123 | Referensi ke data induk karyawan (Employee Data Governance) |
| Jenis Notifikasi | Enum | "Pengingat SLA Cuti", "Info Payroll", "Login Device Baru" | Mengacu ke taksonomi baku (Pre-Cycle) |
| Tingkat Urgensi | Enum | Kritikal / Non-Kritikal | Menentukan apakah preferensi nonaktif berlaku |
| Kanal Pilihan | Enum (multi) | Push App, WhatsApp, SMS | Kosong berarti mengikuti kanal default |
| Status Aktif/Nonaktif | Boolean | Aktif | Hanya dapat "Nonaktif" untuk jenis non-kritikal |
| Tanggal Perubahan Terakhir | Tanggal (YYYY-MM-DD) | 2026-03-10 | Untuk audit perubahan preferensi |

**Post-Cycle**: efektivitas notifikasi (tingkat keterkiriman, tingkat respons per jenis) dievaluasi berkala sebagai bagian Reporting & Analytics Pemakaian ESS (bagian 12); riwayat notifikasi yang sudah terkirim diarsipkan untuk kebutuhan audit/investigasi (dikonsumsi bersama log autentikasi & audit trail unduh, bagian 8-9) dan tidak dihapus meski karyawan menonaktifkan preferensi jenis tersebut ke depannya.

### 7.2 Pengumuman (Broadcast)

**Pre-Cycle**: kebutuhan pengumuman diidentifikasi dari domain sumber (perubahan kebijakan internal, update Kalender Hari Libur, isu K3/*safety alert* site, perubahan pola Roster) beserta penetapan audiens target awal (perusahaan/site/client/departemen) sebelum konten disusun.

**Life Cycle**:
- **Actor**: HR Corporate (lingkup perusahaan), Site Manager (lingkup site) — mengikuti Garis Komando.
- **Proses bisnis**: pesan satu arah ditujukan ke kelompok/populasi (seluruh karyawan, satu site tertentu, satu client tertentu), bukan trigger dari transaksi individual — contoh: perubahan kebijakan internal, update Kalender Hari Libur, pengumuman K3/*safety alert* site tertentu, perubahan pola Roster.
- **Business rule & validasi**: pengumuman memerlukan **tracking keterbacaan** (siapa sudah/belum membaca pengumuman kritikal) — relevan khususnya untuk pengumuman berdampak kepatuhan seperti perubahan SOP client (domain PKS, bagian SOP Change Management); berbeda eksplisit dari notifikasi personal yang tidak memerlukan tracking keterbacaan seketat ini.

**Approval Workflow**: penerbitan pengumuman lingkup perusahaan memerlukan otorisasi HR Corporate; lingkup site cukup Site Manager — penargetan audiens (filter per site/client/departemen) ditetapkan saat pengumuman dibuat.
- **Skenario Setuju** — otorisator (HR Corporate/Site Manager sesuai lingkup) menyetujui konten & audiens target; pengumuman diterbitkan dan tracking keterbacaan mulai berjalan.
- **Skenario Tolak** — konten dinilai tidak sesuai (mis. informasi belum final/berpotensi menimbulkan kepanikan tanpa konteks lengkap, seperti isu perubahan kebijakan yang masih dalam pembahasan); pengumuman tidak diterbitkan, pengaju diminta menyusun ulang.
- **Skenario Perlu Revisi Audiens/Konten** — substansi disetujui namun cakupan audiens perlu disesuaikan (mis. pengumuman yang ditujukan seharusnya per site tertentu, bukan broadcast ke seluruh perusahaan) atau bahasa perlu disesuaikan agar konsisten dengan dukungan multi-bahasa; otorisator mengembalikan dengan catatan sebelum diterbitkan.
- **Skenario Eksepsi/Force Majeure** — untuk pengumuman darurat yang menyangkut keselamatan langsung (mis. insiden K3/keadaan darurat di site tertentu), jalur otorisasi dipercepat: Site Manager dapat menerbitkan seketika tanpa menunggu otorisasi berjenjang penuh, dengan kewajiban notifikasi retroaktif ke HR Corporate segera setelah penerbitan untuk tinjauan pasca-kejadian.

**Post-Cycle**: hasil tracking keterbacaan menjadi dasar tindak lanjut (mis. pengingat ke karyawan yang belum membaca pengumuman kritikal, atau eskalasi ke Site Supervisor untuk memastikan tersampaikan ke karyawan lapangan tanpa akses digital rutin — bagian 4); pengumuman yang sudah diterbitkan diarsipkan sebagai riwayat komunikasi resmi, dapat dirujuk kembali saat audit kepatuhan (mis. pembuktian bahwa perubahan SOP client sudah dikomunikasikan).

### 7.3 Kanal & Infrastruktur Bersama

**Pre-Cycle**: kanal yang tersedia (app/WhatsApp/SMS/kiosk, bagian 4) sudah disiapkan & dipetakan per site sebagai prasyarat sebelum notifikasi maupun pengumuman dapat disalurkan.

**Life Cycle**:
- **Actor**: IT (pemeliharaan infrastruktur kanal), HR Ops (konfigurasi penyaluran).
- **Proses bisnis**: kedua jenis komunikasi (notifikasi bagian 7.1, pengumuman bagian 7.2) berbagi infrastruktur kanal yang sama, namun disajikan terpisah di Unified Dashboard/Inbox (bagian 6) agar karyawan tidak bingung membedakan pesan yang butuh tindakan pribadi (notifikasi) dari sekadar informasi umum (pengumuman).
- **Business rule & validasi**: penambahan/perubahan kanal infrastruktur bersama (mis. penggantian penyedia WhatsApp Business API) berdampak serentak ke kedua jenis komunikasi — perubahan wajib diuji terhadap keduanya sebelum diberlakukan, bukan hanya salah satu.

**Approval Workflow**: penggunaan kanal bersama untuk mengirim notifikasi/pengumuman rutin tidak memerlukan approval tersendiri (mengikuti approval masing-masing di bagian 7.1/7.2); perubahan infrastruktur kanal itu sendiri (penambahan/penggantian penyedia kanal) memerlukan otorisasi IT & HR Corporate.

**Post-Cycle**: kinerja kanal (tingkat keberhasilan pengiriman per kanal) menjadi input Reporting & Analytics Pemakaian ESS (bagian 12) dan basis evaluasi kanal alternatif (bagian 4).

---

## 8. Akses Dokumen Pribadi & Payslip

**Pre-Cycle**: dokumen sumber (slip gaji dari domain Payroll, bukti potong pajak 1721-A1 dari domain After-Cycle, Dokumen Penempatan dari domain Kontrak Kerja, dokumen lain di Document Vault dari domain Employee Data Governance) sudah dihasilkan & tersedia di domain asalnya masing-masing sebelum dapat diakses lewat ESS.

**Life Cycle**:
- **Actor**: karyawan (pengunduh), sistem (penegakan kontrol akses & pencatatan audit trail).
- **Proses bisnis**: titik unduh terpusat untuk slip gaji, bukti potong pajak (1721-A1, domain After-Cycle), Dokumen Penempatan (domain Kontrak Kerja), serta dokumen di Document Vault (domain Employee Data Governance) — dengan kontrol akses & audit trail unduh untuk dokumen sensitif (siapa mengunduh, kapan), mengikuti prinsip audit trail unduh yang sama seperti Document Vault per Karyawan pada domain Employee Data Governance.
- **Business rule & validasi**: karyawan hanya dapat mengunduh dokumen miliknya sendiri, mengikuti cakupan akses per status (bagian 11).

**Approval Workflow**: pengunduhan dokumen pribadi oleh karyawan sendiri tidak memerlukan approval — cukup melalui kontrol akses & autentikasi (bagian 9); permintaan akses ke dokumen di luar cakupan standar (mis. duplikat dokumen lama yang tidak lagi ditampilkan default) memerlukan pengajuan tiket ke HR Ops/IT Helpdesk untuk ditinjau sebelum disediakan.

**Integrasi antar tahap**: bergantung pada dokumen sumber yang dihasilkan domain asal (Payroll, After-Cycle, Kontrak Kerja, Employee Data Governance) — ESS murni menjadi titik akses, bukan pemroses/penghasil dokumen. **Jalur kegagalan**: jika dokumen sumber (mis. payslip periode berjalan atau 1721-A1 tahun pajak tertentu) belum tersedia dari domain asal saat karyawan mencoba mengunduhnya — mis. proses payroll run/pencetakan bukti potong pajak di domain asal belum selesai — ESS wajib menampilkan status "belum tersedia" beserta estimasi waktu ketersediaan (jika diketahui dari domain asal), bukan menampilkan error generik atau halaman kosong yang membingungkan karyawan.

**Notifikasi/trigger**: notifikasi ke karyawan saat dokumen baru tersedia untuk diunduh (mis. slip gaji periode berjalan sudah terbit, 1721-A1 tahun pajak baru sudah tersedia).

**Dokumen/output**: dokumen yang diunduh, log audit trail unduh (dikonsumsi bersama log autentikasi, bagian 9).

**Post-Cycle**: audit trail unduh menjadi bagian bukti kepatuhan & basis investigasi jika terjadi dugaan akses tidak sah terhadap dokumen sensitif, konsisten dengan siklus keamanan berkelanjutan (bagian 9).

---

## 9. Sub-Siklus: Autentikasi & Keamanan Akun Individual

**Pre-Cycle**: kebijakan keamanan akun (password/PIN/biometrik pada aplikasi mobile, *device binding*, kebijakan sesi) ditetapkan sebagai baku.

**Life Cycle**:
- **Actor**: sistem (penegakan kebijakan), IT Helpdesk (reset akses).
- **Proses bisnis**: login mengikuti kebijakan keamanan yang berlaku; permintaan reset akses ditangani melalui IT Helpdesk (domain IT Asset & System Access Provisioning).
- **Business rule & validasi**: kebijakan keamanan dibedakan eksplisit untuk device pribadi (BYOD, mayoritas karyawan) vs device kerja perusahaan (khusus penempatan WFH/Remote, domain IT Asset & System Access Provisioning) — device kerja perusahaan tunduk pada kebijakan keamanan yang lebih ketat mengingat risiko akses data client yang lebih tinggi pada penempatan WFH/Remote.

**Approval Workflow**: reset akses standar (lupa password/PIN) via IT Helpdesk tidak memerlukan approval berjenjang — cukup verifikasi identitas dasar oleh IT Helpdesk (mis. konfirmasi data personal/OTP) sebelum reset dieksekusi; reset akses yang terkait insiden keamanan (dugaan akses tidak sah, laporan kehilangan device) memerlukan eskalasi tambahan ke IT Security/HR Ops sebelum akses baru diberikan, mengingat potensi risiko data client pada penempatan WFH/Remote.

**Notifikasi/trigger**: notifikasi ke karyawan saat terjadi login dari device/lokasi baru (sebagai lapisan keamanan tambahan).

**Pertimbangan Non-Fungsional**: kebijakan keamanan wajib tetap dapat ditegakkan pada skala ribuan akun aktif lintas ratusan site tanpa menambah friksi berlebihan bagi karyawan dengan literasi digital rendah (menyeimbangkan keamanan dengan aksesibilitas, konsisten dengan prinsip desain inklusif bagian 4) — mis. autentikasi dua faktor tetap tersedia dalam bentuk yang tidak bergantung sepenuhnya pada smartphone canggih/email pribadi yang mungkin tidak dimiliki seluruh karyawan.

**Data/field — Log Autentikasi**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Karyawan | String (ref.) | EMP-00123 | Referensi ke data induk karyawan |
| Waktu Login | Tanggal-Waktu (YYYY-MM-DD HH:mm:ss) | 2026-08-17 08:12:45 | Zona waktu server terpusat |
| Jenis Device | Enum | Mobile-BYOD, Mobile-Perusahaan, Kiosk | Membedakan kebijakan keamanan yang berlaku |
| Lokasi/IP | String | 114.10.xx.xx / Site Jakarta-01 | Basis deteksi login dari lokasi baru |
| Status Login | Enum | Berhasil, Gagal, Diblokir | Gagal berulang dapat memicu pemblokiran sementara |
| Metode Autentikasi | Enum | Password, PIN, Biometrik, OTP | Sesuai kebijakan device (BYOD vs perusahaan) |

**Dokumen/output**: log autentikasi, dikonsumsi bersama audit trail unduh dokumen (bagian 8) untuk kebutuhan investigasi jika terjadi dugaan akses tidak sah.

**Post-Cycle**: insiden keamanan akun (percobaan login mencurigakan, laporan kehilangan device) memicu proses reset paksa & investigasi melalui IT Helpdesk.

---

## 10. Sub-Siklus: Consent & Kepatuhan Data Pribadi dari Sisi Karyawan

**Pre-Cycle**: titik-titik pengumpulan data karyawan yang memerlukan *consent* eksplisit diidentifikasi secara konsolidasi — sebelumnya tersebar tanpa kerangka tunggal: absensi berbasis geolokasi (domain Field Workforce Operational Cycle), rekaman teleinterview (domain rekrutmen), monitoring aktivitas untuk WFH (domain IT Asset & System Access Provisioning).

**Life Cycle**:
- **Actor**: karyawan (pemberi consent), HR Ops/fungsi kepatuhan data pribadi (pengelola kebijakan consent).
- **Proses bisnis**: setiap titik pengumpulan data di atas menyertakan mekanisme consent eksplisit yang dikonsolidasikan pencatatannya di satu tempat (bukan tersebar per modul sumber data dengan format berbeda-beda), sehingga karyawan dapat meninjau riwayat consent yang pernah diberikan dari satu titik akses di ESS.
- **Business rule & validasi**: bagian ini melengkapi pembahasan consent yang sudah ada untuk sisi client pada domain Client Portal (bagian Kepatuhan Data Pribadi) dan melengkapi kebijakan klasifikasi field yang ditetapkan pada domain Employee Data Governance (bagian Kontrol Akses Identitas di Client Portal) — ketiganya adalah tiga sisi berbeda dari kepatuhan Undang-Undang Nomor 27 Tahun 2022 tentang Pelindungan Data Pribadi yang sama: **sisi karyawan sebagai subjek data** (di sini), **sisi client sebagai penerima data terbatas** (domain Client Portal), dan **kebijakan klasifikasi field yang mengatur keduanya** (domain Employee Data Governance) — ketiganya saling melengkapi dan merujuk balik satu sama lain, bukan mendefinisikan ulang secara terpisah.

**Integrasi antar tahap**: mekanisme consent ini terhubung ke domain sumber pengumpulan data (Field Workforce Operational Cycle untuk geolokasi absensi, rekrutmen untuk rekaman teleinterview, IT Asset & System Access Provisioning untuk monitoring WFH) — pencatatan consent di ESS wajib mendahului atau berjalan bersamaan dengan pengumpulan data aktual di domain sumber, bukan menyusul setelahnya. **Jalur kegagalan**: jika ditemukan bahwa data sudah terkumpul di domain sumber sebelum consent-nya tercatat di ESS (urutan terbalik) — mis. akibat proses onboarding modul baru yang belum menyertakan mekanisme consent sejak awal — kondisi ini wajib ditangani sebagai insiden kepatuhan yang dieskalasi ke fungsi kepatuhan data pribadi untuk peninjauan retroaktif (memastikan dasar hukum lain yang sah tetap ada, atau consent diminta ulang secara eksplisit), bukan dibiarkan sebagai *gap* pencatatan yang baru terdeteksi saat audit.

**Approval Workflow**: penarikan consent oleh karyawan (jika secara hukum dimungkinkan untuk jenis data tertentu) memerlukan tinjauan fungsi kepatuhan data pribadi sebelum keputusan final diambil, dengan sub-langkah tinjauan sebagai berikut:
1. **Cek jenis data** — mengidentifikasi jenis data spesifik yang consent-nya diminta ditarik (mis. geolokasi absensi, rekaman teleinterview, monitoring aktivitas WFH).
2. **Cek dasar hukum wajib** — memeriksa apakah jenis data tersebut menjadi syarat wajib pelaksanaan hubungan kerja/kepatuhan hukum (mis. dasar verifikasi kehadiran untuk keperluan payroll & billing ke client) atau bersifat opsional.
3. **Cek dampak operasional** — jika bukan syarat wajib mutlak, memeriksa ketersediaan mekanisme alternatif yang setara (mis. metode verifikasi kehadiran lain selain geolokasi) sebelum penarikan dapat diakomodasi.
4. **Keputusan** — hasil ketiga sub-langkah di atas menentukan keputusan akhir sesuai skenario berikut.

- **Skenario Setuju (Penarikan Diterima)** — fungsi kepatuhan data pribadi mengonfirmasi penarikan consent dapat diakomodasi tanpa mengganggu kewajiban kontraktual/hukum lain (mis. jenis data yang ditarik consent-nya bukan syarat mutlak verifikasi kehadiran, ada mekanisme alternatif); penarikan diterapkan efektif dan dicatat dalam riwayat consent.
- **Skenario Tolak (Data Wajib untuk Kepentingan Kontraktual/Hukum)** — penarikan consent untuk data yang menjadi syarat wajib pelaksanaan hubungan kerja atau kepatuhan hukum (mis. data yang menjadi dasar verifikasi kehadiran untuk keperluan payroll & billing ke client) tidak dapat diakomodasi penuh; karyawan diberi penjelasan konsekuensi dan opsi yang tersedia (mis. mekanisme verifikasi kehadiran alternatif jika ada), bukan penarikan tanpa syarat.
- **Skenario Perlu Klarifikasi** — permintaan penarikan tidak jelas cakupannya (mis. karyawan ingin menarik consent untuk sebagian penggunaan data tapi tidak seluruhnya); fungsi kepatuhan data pribadi menghubungi karyawan untuk memperjelas cakupan sebelum keputusan final diambil.

**Notifikasi/trigger**: notifikasi ke karyawan setiap kali ada permintaan consent baru (misal modul baru yang memerlukan pengumpulan data tambahan).

**Data/field — Riwayat Consent**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| ID Karyawan | String (ref.) | EMP-00123 | Referensi ke data induk karyawan |
| Jenis Pengumpulan Data | Enum | Geolokasi Absensi, Rekaman Teleinterview, Monitoring WFH | Mengacu ke titik pengumpulan data yang teridentifikasi (Pre-Cycle) |
| Status Consent | Enum | Diberikan, Ditarik, Ditarik Sebagian | Ditarik Sebagian hasil skenario Perlu Klarifikasi |
| Tanggal Consent Diberikan | Tanggal (YYYY-MM-DD) | 2025-01-15 | |
| Tanggal Penarikan (jika ada) | Tanggal (YYYY-MM-DD) | 2026-06-01 | Kosong jika belum pernah ditarik |
| Dasar Hukum Terkait | String | Pasal ... UU PDP / Klausul PKS | Hasil sub-langkah "cek dasar hukum wajib" |

**Dokumen/output**: riwayat consent per karyawan per jenis pengumpulan data, dapat ditinjau kembali oleh karyawan kapan saja dari ESS.

**Post-Cycle**: riwayat consent menjadi bagian bukti kepatuhan yang dapat ditunjukkan saat audit kepatuhan data pribadi internal/eksternal.

---

## 11. Cakupan Akses per Status: Kandidat vs Karyawan Aktif vs Alumni

| Status | Cakupan Akses |
|---|---|
| Kandidat | Hanya Applicant Tracking (domain rekrutmen) |
| Karyawan Aktif | Akses penuh sesuai peta modul (bagian 3) |
| Alumni | Akses terbatas pasca-offboarding untuk kebutuhan administratif (unduh 1721-A1, dokumen pasca-kerja — domain After-Cycle), dengan masa berlaku akses alumni yang ditetapkan (tidak selamanya aktif) |

**Pembedahan Transisi Status**:
- **Kandidat → Karyawan Aktif** — *Pre-kondisi*: proses rekrutmen selesai & status di Employee Data Governance berubah menjadi aktif (domain rekrutmen, Onboarding). *Proses berjalan*: cakupan akses ESS otomatis meluas dari Applicant Tracking saja menjadi akses penuh sesuai peta modul (bagian 3), berbarengan dengan proses Digital Onboarding ke ESS (bagian 5). *Dampak lanjutan*: karyawan mulai dapat mengajukan seluruh transaksi ESS (cuti, cash advance, dst) dan masuk cakupan siklus keamanan akun (bagian 9) serta consent kepatuhan data pribadi (bagian 10) yang berlaku untuk karyawan aktif.
- **Karyawan Aktif → Alumni** — *Pre-kondisi*: proses offboarding selesai & status di Employee Data Governance berubah menjadi alumni. *Proses berjalan*: cakupan akses ESS otomatis menyempit dari akses penuh menjadi akses terbatas kebutuhan administratif (unduh 1721-A1, dokumen pasca-kerja), dengan masa berlaku akses alumni yang ditetapkan. *Dampak lanjutan*: akses ke modul transaksional aktif (pengajuan cuti, dst) dicabut; setelah masa berlaku akses alumni terlampaui, akses ditutup sepenuhnya kecuali ada kebutuhan administratif lanjutan yang ditangani lewat jalur di luar ESS (mis. permintaan tertulis ke HR).

**Approval Workflow**: transisi cakupan akses tidak memerlukan approval terpisah di lapisan ESS — mengikuti otomatis status pada domain Employee Data Governance sebagai source of truth; hanya perpanjangan masa berlaku akses alumni di luar ketentuan baku (kasus khusus) yang memerlukan otorisasi HR Corporate.

**Notifikasi/trigger**: notifikasi ke karyawan saat cakupan aksesnya berubah (mis. saat status aktif tercatat dan akses penuh terbuka, atau saat mendekati akhir masa berlaku akses alumni sebagai pengingat sebelum akses ditutup).

**Business rule & validasi**: transisi status akses (kandidat → aktif → alumni) mengikuti perubahan status pada domain Employee Data Governance secara otomatis — tidak ada penyesuaian manual terpisah untuk hak akses ESS setiap kali status karyawan berubah. **Jalur jika sinkronisasi status tertunda** (mis. status offboarding sudah tercatat di Employee Data Governance tapi hak akses ESS belum ter-update ke cakupan alumni): dianggap *drift* yang wajib direkonsiliasi segera (mengikuti mekanisme Konsistensi Lintas Sistem pada domain Employee Data Governance) — karyawan yang sudah offboarding tidak boleh tetap memiliki akses penuh lebih dari jendela waktu rekonsiliasi yang wajar, mengingat implikasi keamanan data.

---

## 12. Sub-Siklus: Reporting & Analytics Pemakaian ESS

**Pre-Cycle**: data pemakaian per kanal & per site terkumpul dari aktivitas rutin (bagian 4-9).

**Life Cycle**:
- **Actor**: HR Ops/IT (evaluator).
- **Proses bisnis**: tingkat adopsi/utilisasi dipantau per kanal dan per site sebagai indikator kesenjangan digital yang perlu ditindaklanjuti — site dengan pemakaian aplikasi rendah mengindikasikan masalah device/konektivitas (bukan ketidaktahuan semata), dan memerlukan strategi kanal alternatif (bagian 4).
- **Business rule**: pola ini melengkapi pola Reporting & Analytics yang sudah ada di domain lain (misal General Affairs) — metodologi evaluasi adopsi digunakan konsisten lintas domain yang memiliki masalah kesenjangan digital serupa.

**Approval Workflow**: publikasi/akses dashboard adopsi tidak memerlukan approval — bersifat reporting rutin; hanya perubahan strategi kanal alternatif hasil evaluasi (bagian 4) yang memerlukan otorisasi HR Ops/IT sebelum diterapkan.

**Notifikasi/trigger**: notifikasi ke HR Ops/IT saat adopsi suatu site turun di bawah ambang batas yang ditetapkan, sebagai pemicu tinjauan strategi kanal alternatif.

**Data/field — Data Adopsi per Kanal/Site**:

| Nama Field | Tipe Data | Contoh Nilai | Keterangan |
|---|---|---|---|
| Kode Site | String (ref.) | SITE-JKT-01 | Referensi ke data induk site |
| Kanal | Enum | App Mobile, WhatsApp, Kiosk, SMS | |
| Periode | Tanggal (YYYY-MM) | 2026-07 | Periode agregasi bulanan |
| Jumlah Karyawan Aktif di Site | Numerik | 245 | Basis penghitungan persentase adopsi |
| Jumlah Pengguna Aktif Kanal | Numerik | 180 | Karyawan yang benar-benar memakai kanal pada periode terkait |
| Persentase Adopsi | Numerik (%) | 73.5 | Jumlah Pengguna Aktif ÷ Jumlah Karyawan Aktif |

**Dokumen/output**: dashboard adopsi/utilisasi per kanal/site.

**Post-Cycle**: hasil evaluasi menjadi input revisi strategi kanal alternatif (bagian 4) periode berikutnya — menutup satu putaran evaluasi-penyesuaian.

---

## 13. Ringkasan Ketentuan Kunci

- ESS adalah lapisan antarmuka terpadu, bukan pemilik logika bisnis baru — setiap perubahan proses di domain asal (cuti, resign, cash advance, dst) otomatis tercermin di ESS tanpa memerlukan perubahan terpisah di lapisan ini.
- Desain multi-kanal (app/WhatsApp/kiosk/low-bandwidth) wajib mengasumsikan populasi karyawan lapangan lebih rentan kesenjangan digital dibanding pengguna Client Portal — bukan diasumsikan setara.
- Unified Dashboard/Inbox adalah padanan Unified Client Approval Queue di sisi karyawan/approver internal — dua permukaan terpisah dengan prinsip desain sama, disesuaikan populasi pengguna masing-masing.
- Notifikasi (personal/transaksional) dan Pengumuman (broadcast) adalah dua jenis komunikasi berbeda karakter — pengumuman memerlukan tracking keterbacaan eksplisit, notifikasi kritikal tidak dapat dinonaktifkan meski karyawan mengatur preferensi notifikasi non-kritikal.
- Consent data pribadi dari sisi karyawan adalah satu dari tiga sisi kepatuhan UU PDP yang saling melengkapi dengan sisi client (Client Portal) dan kebijakan klasifikasi field (Employee Data Governance) — penarikan consent untuk data yang menjadi syarat kontraktual/hukum wajib tidak dapat diakomodasi penuh tanpa opsi alternatif yang jelas ke karyawan.
- Transisi cakupan akses (kandidat–aktif–alumni) mengikuti status di Employee Data Governance secara otomatis; keterlambatan sinkronisasi status adalah insiden keamanan yang wajib direkonsiliasi segera, bukan dibiarkan sampai siklus berikutnya.

---

## 14. Walkthrough Naratif Proses (Cycle → Proses → Aktivitas)

| Cycle | Proses | Aktivitas | Actor | Approval Chain | Klausul PKS Terkait | ID Requirement Terkait |
|---|---|---|---|---|---|---|
| Onboarding Digital | Provisioning & tutorial | Pembuatan kredensial, first-login, tutorial dasar | HR Ops, IT, karyawan baru | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
| Komunikasi Terkonsolidasi | Notifikasi personal | Pengiriman notifikasi transaksional, eskalasi jika kritikal tidak direspons | Sistem | Eskalasi otomatis sesuai SLA domain asal | Tidak berlaku langsung | TBD |
| Komunikasi Terkonsolidasi | Pengumuman broadcast | Penerbitan, penargetan audiens, tracking keterbacaan | HR Corporate/Site Manager | Otorisasi sesuai lingkup (perusahaan/site) | Tidak berlaku langsung | TBD |
| Keamanan Akun | Autentikasi & reset akses | Login sesuai kebijakan, reset via IT Helpdesk | Sistem, IT Helpdesk | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
| Consent Data Pribadi | Pemberian & peninjauan consent | Consent eksplisit per titik pengumpulan data, peninjauan riwayat | Karyawan, fungsi kepatuhan data pribadi | Tinjauan kepatuhan untuk penarikan consent | Tidak berlaku langsung | TBD |
| Reporting Pemakaian | Evaluasi adopsi | Pemantauan adopsi per kanal/site, revisi strategi kanal | HR Ops, IT | Tidak ada approval terpisah | Tidak berlaku langsung | TBD |
